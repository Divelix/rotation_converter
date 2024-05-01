<script setup lang="ts">
import { computed, inject, nextTick, ref, shallowRef, watch, type Ref, type ShallowRef } from 'vue';
import IconCopy from './icons/IconCopy.vue';
import IconEdit from './icons/IconEdit.vue';
import IconYes from './icons/IconYes.vue';
import IconNo from './icons/IconNo.vue';
import ActionButton from './ActionButton.vue';
import { Quaternion } from 'three';
import { ToastType } from '@/types';
import * as Constants from '@/const';

const COPY_TEXT = "Copied quaternion to clipboard"
const PARSE_ERROR_TEXT = "Quaternion parsing error"
const NORM_ERROR_TEXT = "Quaternion must be normalized"

const isEdit: ShallowRef<Boolean> = inject("isEdit")!
const toastMsg: ShallowRef<String> = inject("toastMsg")!
const toastType: ShallowRef<ToastType> = inject("toastType")!
const isQuatApply: ShallowRef<Boolean> = inject("isQuatApply")!
const quat: Ref<number[]> = inject("quat")!
const isError: ShallowRef<Boolean> = shallowRef(false)
const isEditQuat: ShallowRef<Boolean> = shallowRef(false)
const quatStr = shallowRef("")
const textarea: Ref<HTMLTextAreaElement | null> = ref(null)

const isPositiveArr = computed(() => {
    return quat.value.map(value => {
        return value >= -Constants.SMALL_NUMBER
    })
})
const absArr = computed(() => {
    return quat.value.map(value => {
        return Math.abs(value)
    })
})
const closeToOneArr = computed(() => {
    return quat.value.map(value => {
        return 1 - Math.abs(value) < Constants.SMALL_NUMBER
    })
})

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

const isNormalized = (nums: number[]): boolean => {
    const quat = new Quaternion().fromArray(nums)
    return Math.abs(quat.lengthSq() - 1) < Constants.SMALL_NUMBER
}

function applyQuat() {
    const nums = parse4floats(quatStr.value) // parse numbers from string
    if (!nums) {
        // throw SyntaxError("Parse error: matrix format is incorrect")
        toastMsg.value = PARSE_ERROR_TEXT
        toastType.value = ToastType.ERROR
        isError.value = true
    }
    const isNorm = isNormalized(nums!) // check if correct rotation matrix  
    if (!isNorm) {
        // throw SyntaxError("Rotation matrix MUST be orthonormal")
        toastMsg.value = NORM_ERROR_TEXT
        toastType.value = ToastType.ERROR
        isError.value = true
    }
    if (!isError.value) {
        quat.value = nums!
        isEditQuat.value = false
        isQuatApply.value = true
    } else {
        setTimeout(() => isError.value = false, 1000)
    }
}

function updateQuatStr(nums: number[]) {
    quatStr.value = '[\n';
    for (let i = 0; i < nums.length; i++) {
        if (i % 4 !== 0) {
            quatStr.value += ',\n';
        }
        quatStr.value += "  "
        quatStr.value += nums[i].toFixed(Constants.COPY_PRECISION);
    }
    quatStr.value += '\n]';
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
            <div class="quat-grid">
                <div v-for="(axis, i) in ['x', 'y', 'z', 'w']" :key="i" :class="axis">
                    <div class="axis-value" :class="{ one: closeToOneArr[i] }">
                        <span :class="{ invisible: isPositiveArr[i] }">-</span>
                        {{ absArr[i].toFixed(Constants.DISPLAY_PRECISION) }}
                    </div>
                    <div class="axis-label">{{ axis.toUpperCase() }}</div>
                </div>
            </div>
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
h2 {
    color: white;
}

.quat {
    background-color: var(--c-dark-2);
    border-radius: var(--cont-border-radius);
    padding: 10px;
    display: flex;
    flex-direction: column;
    place-content: center;
    align-items: center;
}

.content {
    display: flex;
    align-items: center;
    gap: 1em;
}

.quat-grid {
    display: flex;
    user-select: none;
}

.axis-value {
    padding: 10px;
    font-size: 20pt;
}

.axis-label {
    border-radius: 0 0 50px 50px;
    text-align: center;
    padding: 10px;
    font-size: 14pt;
}

.x .axis-label {
    background-color: var(--c-bg-quat-x);
}

.y .axis-label {
    background-color: var(--c-bg-quat-y);
}

.z .axis-label {
    background-color: var(--c-bg-quat-z);
}

.w .axis-label {
    background-color: var(--c-bg-quat-w);
}

.content {
    display: flex;
    gap: 5px;
}

textarea {
    resize: none;
    background-color: #282828;
    border-color: #414141;
    border-radius: 10px;
    color: white;
    width: 200px;
    height: 120px;
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
    color: white;
}

.error {
    border-color: red;
}
</style>
