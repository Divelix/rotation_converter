<script setup lang="ts">
import { shallowRef } from 'vue';
import Toast from './Toast.vue'

const props = defineProps({
    toast: String
})
const emit = defineEmits(['btnClick'])
const showToast = shallowRef(false)
const TOAST_TIME_MS = 1000

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
        <Toast v-if="showToast" :msg="props.toast" />
    </transition>
</template>

<style scoped>
button {
    width: 50px;
    height: 50px;
    text-align: center;
    justify-content: center;
    padding: 10px;
    background-color: #282828;
    border-radius: 20%;
    border: 1px solid var(--c-divider-dark-1);
    cursor: pointer;
}

button:hover {
    background-color: #333;
}

button:active {
    background-color: #555;
}

/* Toast animation */
.toast-enter-from {
    opacity: 0;
    transform: translateY(60px);
}

.toast-enter-to {
    opacity: 1;
    transform: translateY(0);
}

.toast-enter-active {
    transition: all 0.3s ease;
}

.toast-leave-from {
    opacity: 1;
    transform: translateY(0);
}

.toast-leave-to {
    opacity: 0;
    transform: translateY(60px);
}

.toast-leave-active {
    transition: all 0.3s ease;
}
</style>