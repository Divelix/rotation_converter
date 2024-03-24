<script setup lang="ts">
import CoordControl from './CoordControl.vue';

</script>

<template>
    <div class="vis_controls">
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
}
</style>