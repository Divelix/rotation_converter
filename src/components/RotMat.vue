<script setup lang="ts">
import { computed, inject, shallowRef, watch, type Ref, type ShallowRef } from 'vue';
import IconCopy from './icons/IconCopy.vue';
import IconEdit from './icons/IconEdit.vue';
import IconYes from './icons/IconYes.vue';
import IconNo from './icons/IconNo.vue';
import ActionButton from './ActionButton.vue';

const isEdit: ShallowRef<Boolean> = inject("isEdit")!
const rotMat: Ref<Array<number>> = inject("rotMat")!
const isEditMat: ShallowRef<Boolean> = shallowRef(false)
const displayPrecision = 3
const copyPrecision = 5
const smallNumber = 0.0000000001
const isPositiveArr = computed(() => {
    return rotMat.value.map(value => {
        return value >= -smallNumber
    })
})
const absArr = computed(() => {
    return rotMat.value.map(value => {
        return Math.abs(value)
    })
})
const closeToOneArr = computed(() => {
    return rotMat.value.map(value => {
        return 1 - Math.abs(value) < smallNumber
    })
})
const matArrStr = computed(() => {
    const numArr = rotMat.value
    let result = '[\n';
    for (let i = 0; i < numArr.length; i++) {
        if (i % 3 === 0) {
            if (i !== 0) {
                result += '],\n';
            }
            result += '    [';
        }
        if (i % 3 !== 0) {
            result += ',';
        }
        result += numArr[i].toFixed(copyPrecision);
    }
    result += ']\n]';
    return result
})

function copyMat() {
    navigator.clipboard.writeText(matArrStr.value)
}
const openEdit = () => isEditMat.value = true
const closeEdit = () => isEditMat.value = false
function applyMat() {
    // TODO: parse numbers from string
    // TODO: check if correct matrix  
    // TODO: set to rotMat ref
    // TODO: turn off EDIT mode
}

watch(isEditMat, (newIsEditMat) => {
    isEdit.value = newIsEditMat
})
</script>

<template>
    <div>
        <h2>Rotation Matrix</h2>
        <div class="content" v-if="isEditMat">
            <textarea pattern="[0-9.,\[\]]+">{{ matArrStr }}</textarea>
            <div class="action-buttons">
                <ActionButton @btn-click="applyMat" toast="Apply new matrix">
                    <template #icon>
                        <IconYes />
                    </template>
                </ActionButton>
                <ActionButton @btn-click="closeEdit" toast="Close edit">
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
                            {{ absArr[rowIndex * 3 + colIndex].toFixed(displayPrecision) }}
                        </div>
                    </td>
                </tr>
            </table>
            <div class="action-buttons">
                <ActionButton @btn-click="copyMat" toast="Copied to clipboard">
                    <template #icon>
                        <IconCopy />
                    </template>
                </ActionButton>
                <ActionButton @btn-click="openEdit" toast="Edit">
                    <template #icon>
                        <IconEdit />
                    </template>
                </ActionButton>
            </div>
        </div>
    </div>
</template>

<style scoped>
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
</style>
