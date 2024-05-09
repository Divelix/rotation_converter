<script setup lang="ts">
import { shallowRef } from 'vue';
import IconGithub from './icons/IconGithub.vue';

const isActive = shallowRef(false)
</script>

<template>
    <div class="links">
        <a href="https://github.com/Divelix/rotation_converter" target="_blank">
            <IconGithub />
        </a>
    </div>
</template>

<style scoped>
.links {
    position: fixed;
    z-index: 1;
    top: 35px;
    right: 85px;
}

a {
    display: inline-block;
    width: 30px;
    height: 30px;
    border-radius: 50%;
    cursor: pointer;
}

svg {
    fill: var(--c-svg);
    transition: fill 0.3s;
}

svg:hover {
    fill: var(--c-svg-hover);
}
</style>