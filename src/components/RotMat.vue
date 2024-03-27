<script setup lang="ts">
import { computed, inject, type Ref } from 'vue';

const rotMat: Ref<Array<number>> = inject("rotMat")!
const precision = 2
const isPositiveArr = computed(() => {
    return rotMat.value.map(value => {
        return value >= -0.00001
    })
})
const absArr = computed(() => {
    return rotMat.value.map(value => {
        return Math.abs(value)
    })
})
</script>

<template>
    <div>
        <h2>Rotation Matrix</h2>
        <table>
            <tr v-for="(row, rowIndex) in [0, 1, 2]" :key="rowIndex">
                <td v-for="(col, colIndex) in [0, 1, 2]" :key="colIndex">
                    <span :class="{ invisible: isPositiveArr[rowIndex * 3 + colIndex] }">-</span>
                    {{ absArr[rowIndex * 3 + colIndex].toFixed(precision) }}
                </td>
            </tr>
        </table>
    </div>
</template>

<style scoped>
.invisible {
    opacity: 0.0;
}

table {
    border-collapse: collapse;
    white-space: pre-wrap;
}

td {
    border: 1px solid #ccc;
    padding: 8px;
    text-align: center;
}
</style>