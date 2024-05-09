<script setup lang="ts">
import { computed, inject, nextTick, ref, shallowRef, watch, type Ref, type ShallowRef } from 'vue';
import IconCopy from './icons/IconCopy.vue';
import IconEdit from './icons/IconEdit.vue';
import IconYes from './icons/IconYes.vue';
import IconNo from './icons/IconNo.vue';
import ActionButton from './ActionButton.vue';
import { Matrix3 } from 'three';
import { ToastType } from '@/types';
import * as Constants from '@/const';

const COPY_TEXT = "Copied matrix to clipboard"
const PARSE_ERROR_TEXT = "Matrix parsing error"
const ORTHO_ERROR_TEXT = "Matrix must be orthonormal"

const isEdit: ShallowRef<Boolean> = inject("isEdit")!
const toastMsg: ShallowRef<String> = inject("toastMsg")!
const toastType: ShallowRef<ToastType> = inject("toastType")!
const isError: ShallowRef<Boolean> = shallowRef(false)
const isEditMat: ShallowRef<Boolean> = shallowRef(false)
const isMatApply: ShallowRef<Boolean> = inject("isMatApply")!
const rotMat: Ref<number[]> = inject("rotMat")!
const matStr = shallowRef("")
const textarea: Ref<HTMLTextAreaElement | null> = ref(null)

const isPositiveArr = computed(() => {
    return rotMat.value.map(value => {
        return value >= -Constants.SMALL_NUMBER
    })
})
const absArr = computed(() => {
    return rotMat.value.map(value => {
        return Math.abs(value)
    })
})
const closeToOneArr = computed(() => {
    return rotMat.value.map(value => {
        return 1 - Math.abs(value) < Constants.SMALL_NUMBER
    })
})

const copyMat = () => {
    navigator.clipboard.writeText(matStr.value)
    toastMsg.value = COPY_TEXT
    toastType.value = ToastType.INFO
}
const openEdit = () => isEditMat.value = true
const closeEdit = () => isEditMat.value = false
const parse9floats = (input: String): number[] | null => {
    const regex: RegExp = /[-+]?[0-9]*\.?[0-9]+([eE][-+]?[0-9]+)?/g;
    const matches: RegExpMatchArray | null = input.match(regex);
    if (!matches) return null
    const nums = matches.map(match => parseFloat(match));
    if (nums.length != 9) return null
    return nums
}

const isOrthNorm = (nums: number[]): boolean => {
    const mat = new Matrix3().fromArray(nums)
    // Check if orthogonal
    const product = mat.multiply(mat.clone().transpose())
    const areEqual = (a1: number[], a2: number[]) => a1.every((val, idx) => val === a2[idx])
    const isOrthogonal = areEqual(product.elements, new Matrix3().identity().elements)

    // Check if normal
    const isNormal = Math.abs(mat.determinant() - 1) < Constants.SMALL_NUMBER
    return isOrthogonal && isNormal
}

function applyMat() {
    const nums = parse9floats(matStr.value) // parse numbers from string
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
        rotMat.value = nums!
        isEditMat.value = false
        isMatApply.value = true
    } else {
        setTimeout(() => isError.value = false, 1000)
    }
}

function updateMatStr(nums: number[]) {
    matStr.value = '[\n';
    for (let i = 0; i < nums.length; i++) {
        if (i % 3 === 0) {
            if (i !== 0) {
                matStr.value += '],\n';
            }
            matStr.value += '  [';
        }
        if (i % 3 !== 0) {
            matStr.value += ', ';
        }
        matStr.value += nums[i].toFixed(Constants.COPY_PRECISION);
    }
    matStr.value += ']\n]';
}

watch(isEditMat, (newIsEditMat) => {
    isEdit.value = newIsEditMat
    updateMatStr(rotMat.value)
    if (newIsEditMat) {
        nextTick(() => {
            textarea.value!.focus()
        })
    }
})
watch(rotMat, (newRotMat) => {
    updateMatStr(newRotMat)
})
</script>

<template>
    <div class="rot">
        <h2>Rotation Matrix</h2>
        <div class="content" v-if="isEditMat">
            <textarea ref="textarea" v-model="matStr" @focus="($event.target as HTMLTextAreaElement).select()"
                :class="{ error: isError }"></textarea>
            <div class="action-buttons">
                <ActionButton @btn-click="applyMat">
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
                <tr v-for="(row, rowIndex) in [0, 1, 2]" :key="rowIndex">
                    <td v-for="(col, colIndex) in [0, 1, 2]" :key="colIndex">
                        <div :class="{ one: closeToOneArr[rowIndex * 3 + colIndex] }">
                            <span :class="{ invisible: isPositiveArr[rowIndex * 3 + colIndex] }">-</span>
                            {{ absArr[rowIndex * 3 + colIndex].toFixed(Constants.DISPLAY_PRECISION) }}
                        </div>
                    </td>
                </tr>
            </table>
            <div class="action-buttons">
                <ActionButton @btn-click="copyMat">
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
svg {
    fill: var(--c-svg);
}

h2 {
    color: var(--c-text-accent);
}

.rot {
    background-color: var(--c-bg-accent);
    border-radius: var(--cont-border-radius);
    padding: 10px;
    display: flex;
    flex-direction: column;
    place-content: center;
    align-items: center;
}

.content {
    display: flex;
    gap: 1em;
}

table {
    user-select: none;
}

textarea {
    resize: none;
    background-color: var(--c-btn-bg);
    border-color: var(--c-btn-border);
    border-radius: 10px;
    color: var(--c-text-accent);
    width: 300px;
    height: 100px;
    padding: 10px;
    margin: 10px;
}

.action-buttons {
    display: flex;
    flex-direction: column;
    gap: 1em;
}

.invisible {
    opacity: 0;
}

.one {
    color: var(--c-text-accent);
}

table {
    border-collapse: collapse;
    white-space: pre-wrap;
}

td {
    padding: 8px;
    text-align: center;
    font-size: 20pt;
}

.error {
    border-color: var(--c-error);
}
</style>
