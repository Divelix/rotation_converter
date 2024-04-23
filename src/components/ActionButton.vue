<script setup lang="ts">
import { type ShallowRef, shallowRef, inject } from 'vue';
import Toast from './Toast.vue'

const emit = defineEmits(['btnClick'])
const showToast = shallowRef(false)
const TOAST_TIME_MS = 2000

const triggerToast = () => {
    showToast.value = true
    setTimeout(() => showToast.value = false, TOAST_TIME_MS)
}
const handleClick = () => {
    triggerToast()
    emit('btnClick')
}
</script>

<template>
    <button @click="handleClick">
        <slot name="icon"></slot>
    </button>
    <transition name="toast">
        <Toast v-if="showToast" />
    </transition>
</template>

<style scoped>
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

/* Toast animation */
.toast-enter-from {
    opacity: 0;
    transform: translate(-50%, 60px);
}

.toast-enter-to {
    opacity: 1;
    transform: translate(-50%, 0);
}

.toast-enter-active {
    transition: all 0.3s ease;
}

.toast-leave-from {
    opacity: 1;
    transform: translate(-50%, 0);
}

.toast-leave-to {
    opacity: 0;
    transform: translate(-50%, 60px);
}

.toast-leave-active {
    transition: all 0.3s ease;
}
</style>