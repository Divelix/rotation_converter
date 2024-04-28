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
    fill: var(--c-text-dark-2);
    transition: fill 0.3s;
}

svg:hover {
    fill: white;
}

/* a:hover {
    background-color: var(--c-bg-btn-hover);
}

a:active {
    background-color: var(--c-bg-btn-active);
} */

.content {
    z-index: 1;
    top: 15px;
    right: 15px;
    border: 1px solid var(--c-divider-dark-1);
    background-color: var(--c-bg-btn);
    padding: 0 70px 20px 70px;
    display: flex;
    flex-direction: column;
}

table {
    border-collapse: collapse;
    line-height: 1;
}

.header-row {
    color: white;
    background-color: var(--c-bg-btn-hover);
}

td {
    border: 1px solid var(--c-divider-dark-1);
    padding: 10px;
}
</style>