<script setup lang="ts">
import { computed, inject, type Ref } from 'vue';

const rotMat: Ref<Array<number>> = inject("rotMat")!
const precision = 2
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
</script>

<template>
    <div>
        <h2>Rotation Matrix</h2>
        <table>
            <tr v-for="(row, rowIndex) in [0, 1, 2]" :key="rowIndex">
                <td v-for="(col, colIndex) in [0, 1, 2]" :key="colIndex">
                    <div :class="{ one: closeToOneArr[rowIndex * 3 + colIndex] }">
                        <span :class="{ invisible: isPositiveArr[rowIndex * 3 + colIndex] }">-</span>
                        {{ absArr[rowIndex * 3 + colIndex].toFixed(precision) }}
                    </div>
                </td>
            </tr>
        </table>
    </div>
</template>

<style scoped>
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