<script setup lang="ts">
import { computed, inject, type Ref } from 'vue';
import IconCopy from './icons/IconCopy.vue';
import ActionButton from './ActionButton.vue';

const rotMat: Ref<Array<number>> = inject("rotMat")!
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

function copyMat() {
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
    navigator.clipboard.writeText(result)
}
</script>

<template>
    <div>
        <h2>Rotation Matrix</h2>
        <div class="mat_container">
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
            <ActionButton @click.prevent="copyMat">
                <template #icon>
                    <IconCopy />
                </template>
            </ActionButton>
        </div>
    </div>
</template>

<style scoped>
.mat_container {
    display: flex;
    gap: 5px;
}

.invisible {
    opacity: 0.0;
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
