<script setup lang="ts">
import { inject, type ShallowRef } from 'vue';
import IconReset from './icons/IconReset.vue';
import CoordControl from './CoordControl.vue';

const isLocal = inject("isLocal")!
const needReset: ShallowRef<Boolean> = inject("needReset")!
</script>

<template>
    <div class="vis_controls">
        <div class="tooltip">
            <input type="checkbox" v-model="isLocal">
            <span class="tooltiptext">
                Rotation mod: <br>
                on = local <br>
                off = global
            </span>
        </div>
        <CoordControl axis="x" />
        <CoordControl axis="y" />
        <CoordControl axis="z" />
        <button class="reset" @click="needReset = true">
            <IconReset />
        </button>
    </div>
</template>

<style scoped>
.vis_controls {
    /* background-color: #00ffff77; */
    display: flex;
    flex-direction: column;
    justify-content: space-evenly;
    align-items: center;
}

.vis_controls input[type="checkbox"] {
    width: 30px;
    height: 30px;
}

button {
    width: 50px;
    height: 50px;
    text-align: center;
    justify-content: center;
    padding: 10px;
    border-radius: 20%;
    cursor: pointer;
    border: 1px solid var(--c-divider-dark-1);
    background-color: var(--c-bg-btn);
}

button:hover {
    background-color: var(--c-bg-btn-hover);
}

button:active {
    background-color: var(--c-bg-btn-active);
}
</style>