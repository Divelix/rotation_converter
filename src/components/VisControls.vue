<script setup lang="ts">
import { inject } from 'vue';
import CoordControl from './CoordControl.vue';

const isLocal = inject("isLocal")!
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
</style>