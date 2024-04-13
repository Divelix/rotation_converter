<script setup lang="ts">
import { ToastType } from '@/types';
import { computed, inject, type ShallowRef } from 'vue';

const toastType: ShallowRef<ToastType> = inject("toastType")!
const toastMsg: ShallowRef<String> = inject("toastMsg")!
const isError = computed(() => toastType.value == ToastType.ERROR)
</script>

<template>
    <div class="toast-wrapper">
        <div class="toast" :class="{ error: isError }">
            {{ toastMsg }}
        </div>
    </div>
</template>

<style scoped>
.toast-wrapper {
    position: fixed;
    left: 50%;
    bottom: 20px;
    transform: translate(-50%, 0);
}

.toast {
    background-color: green;
    color: #fff;
    padding: 20px;
    border-radius: 20px;
    text-align: center;
    max-width: 400px;
    margin: 0 auto;
}

.error {
    background-color: red;
}
</style>