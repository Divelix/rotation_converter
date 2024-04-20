<script setup lang="ts">
import { computed, inject, nextTick, ref, shallowRef, watch, type Ref, type ShallowRef } from 'vue';
import IconCopy from './icons/IconCopy.vue';
import IconEdit from './icons/IconEdit.vue';
import IconYes from './icons/IconYes.vue';
import IconNo from './icons/IconNo.vue';
import ActionButton from './ActionButton.vue';
import { Matrix3 } from 'three';
import { ToastType } from '@/types';

const COPY_TEXT = "Copied quaternion to clipboard"
const PARSE_ERROR_TEXT = "Quaternion parsing error"
const ORTHO_ERROR_TEXT = "Quaternion must be orthnormal"

const isEdit: ShallowRef<Boolean> = inject("isEdit")!
const toastMsg: ShallowRef<String> = inject("toastMsg")!
const toastType: ShallowRef<ToastType> = inject("toastType")!
const isError: ShallowRef<Boolean> = shallowRef(false)
const isEditQuat: ShallowRef<Boolean> = shallowRef(false)
const quat: Ref<number[]> = inject("quat")!
const quatStr = shallowRef("")
const textarea: Ref<HTMLTextAreaElement | null> = ref(null)

const isPositiveArr = computed(() => {
    return quat.value.map(value => {
        return value >= -smallNumber
    })
})
const absArr = computed(() => {
    return quat.value.map(value => {
        return Math.abs(value)
    })
})
const closeToOneArr = computed(() => {
    return quat.value.map(value => {
        return 1 - Math.abs(value) < smallNumber
    })
})

const displayPrecision = 3
const copyPrecision = 5
const smallNumber = 0.0001

const copyQuat = () => {
    navigator.clipboard.writeText(quatStr.value)
    toastMsg.value = COPY_TEXT
    toastType.value = ToastType.INFO
}
const openEdit = () => isEditQuat.value = true
const closeEdit = () => isEditQuat.value = false
const parse4floats = (input: String): number[] | null => {
    const regex: RegExp = /[-+]?[0-9]*\.?[0-9]+([eE][-+]?[0-9]+)?/g;
    const matches: RegExpMatchArray | null = input.match(regex);
    if (!matches) return null
    const nums = matches.map(match => parseFloat(match));
    if (nums.length != 4) return null
    return nums
}

const isOrthNorm = (nums: number[]): boolean => {
    const mat = new Matrix3().fromArray(nums)
    // Check if orthogonal
    const productMat = mat.clone().multiply(mat.clone().transpose())
    const isOrthogonal = Math.abs(productMat.determinant() - 1) < smallNumber

    // Check if normal
    const isNormal = Math.abs(mat.determinant() - 1) < smallNumber
    return isOrthogonal && isNormal
}

function applyQuat() {
    const nums = parse4floats(quatStr.value) // parse numbers from string
    if (!nums) {
        // throw SyntaxError("Parse error: matrix format is incorrect")
        toastMsg.value = PARSE_ERROR_TEXT
        toastType.value = ToastType.ERROR
        isError.value = true
    }
    const isRot = isOrthNorm(nums!) // check if correct rotation matrix  
    if (!isRot) {
        // throw SyntaxError("Rotation matrix MUST be orthonormal")
        toastMsg.value = ORTHO_ERROR_TEXT
        toastType.value = ToastType.ERROR
        isError.value = true
    }
    if (!isError.value) {
        quat.value = nums!
        isEditQuat.value = false
    } else {
        setTimeout(() => isError.value = false, 1000)
    }
}

function updateQuatStr(nums: number[]) {
    quatStr.value = '[\n';
    for (let i = 0; i < nums.length; i++) {
        if (i % 3 === 0) {
            if (i !== 0) {
                quatStr.value += '],\n';
            }
            quatStr.value += '    [';
        }
        if (i % 3 !== 0) {
            quatStr.value += ', ';
        }
        quatStr.value += nums[i].toFixed(copyPrecision);
    }
    quatStr.value += ']\n]';
}

watch(isEditQuat, (newIsEditQuat) => {
    isEdit.value = newIsEditQuat
    updateQuatStr(quat.value)
    if (newIsEditQuat) {
        nextTick(() => {
            textarea.value!.focus()
        })
    }
})
watch(quat, (newQuat) => {
    updateQuatStr(newQuat)
})
</script>

<template>
    <div class="quat">
        <h2>Quaternion</h2>
        <div class="content" v-if="isEditQuat">
            <textarea ref="textarea" v-model="quatStr" @focus="($event.target as HTMLTextAreaElement).select()"
                :class="{ error: isError }"></textarea>
            <div class="action-buttons">
                <ActionButton @btn-click="applyQuat">
                    <template #icon>
                        <IconYes />
                    </template>
                </ActionButton>
                <ActionButton @btn-click="closeEdit">
                    <template #icon>
                        <IconNo />
                    </template>
                </ActionButton>
            </div>
        </div>
        <div class="content" v-else>
            <table>
                <tr>
                    <td v-for="(col, colIndex) in [0, 1, 2, 3]" :key="colIndex">
                        <div :class="{ one: closeToOneArr[colIndex] }">
                            <span :class="{ invisible: isPositiveArr[colIndex] }">-</span>
                            {{ absArr[colIndex].toFixed(displayPrecision) }}
                        </div>
                    </td>
                </tr>
                <tr>
                    <td>X</td>
                    <td>Y</td>
                    <td>Z</td>
                    <td>W</td>
                </tr>
            </table>
            <div class="action-buttons">
                <ActionButton @btn-click="copyQuat">
                    <template #icon>
                        <IconCopy />
                    </template>
                </ActionButton>
                <ActionButton @btn-click="openEdit">
                    <template #icon>
                        <IconEdit />
                    </template>
                </ActionButton>
            </div>
        </div>
    </div>
</template>

<style scoped>
.quat {
    display: flex;
    flex-direction: column;
    place-content: center;
}

.content {
    display: flex;
    gap: 5px;
}

table {
    user-select: none;
}

textarea {
    resize: none;
    background-color: #282828;
    border-color: #414141;
    border-radius: 10px;
    color: white;
    width: 300px;
    height: 100px;
}

.action-buttons {
    display: flex;
    flex-direction: column;
    gap: 5px;
}

.invisible {
    opacity: 0;
}

.one {
    color: white;
}

table {
    border-collapse: collapse;
    white-space: pre-wrap;
}

td {
    border: 1px solid #858585;
    padding: 8px;
    text-align: center;
}

.error {
    border-color: red;
}
</style>
