<script setup lang="ts">
import { inject, type Ref } from 'vue';

const props = defineProps({
    axis: String
})

const axisCounters: Ref<number[]> = inject("axisCounters")!
const plus = () => {
    stepCounters(true)
}
const minus = () => {
    stepCounters(false)
}
const stepCounters = (isUp: Boolean) => {
    let s = isUp ? 1 : -1
    switch (props.axis!) {
        case "x":
            axisCounters.value = [s, 0, 0]
            break;
        case "y":
            axisCounters.value = [0, s, 0]
            break;
        case "z":
            axisCounters.value = [0, 0, s]
            break;
        default:
            break;
    }
}
</script>

<template>
    <div :class="axis">
        <button class="plus" @click="plus">+</button>
        <div class="label">
            <span>{{ axis?.toUpperCase() }}</span>
        </div>
        <button class="minus" @click="minus">-</button>
    </div>
</template>

<style scoped>
div {
    font-size: 16pt;
    display: flex;
    flex-direction: column;
    text-align: center;
}

button {
    font-size: 24pt;
    padding-left: 10px;
    padding-right: 10px;
    color: #ffffff;
    /* text-shadow: 0px 0px 5px #000; */
    border-width: 0px;
    cursor: pointer;
}

.plus {
    border-radius: 50% 50% 0% 0%;
}

.minus {
    border-radius: 0% 0% 50% 50%;
}

.x .label {
    background-color: #ff000077;
}

.x button {
    background-color: #ff0000;
}

.y .label {
    background-color: #00ff0077;
}

.y button {
    background-color: #00ff00;
}

.z .label {
    background-color: #0000ff77;
}

.z button {
    background-color: #0000ff;
}
</style>