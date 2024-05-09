<script setup lang="ts">
import { useToggle } from '@vueuse/core'
import IconMoon from './icons/IconMoon.vue'
import IconSun from './icons/IconSun.vue'
import { inject, type WritableComputedRef } from 'vue';

const isDark: WritableComputedRef<Boolean> = inject("isDark")!
const toggleDark = useToggle(isDark)

</script>

<template>
    <div class="mode">
        <button @click="toggleDark()">
            <IconMoon v-if="isDark" />
            <IconSun v-else />
        </button>
    </div>
</template>

<style scoped>
.mode {
    position: fixed;
    z-index: 1;
    top: 33px;
    right: 125px;
}

button {
    width: 40px;
    height: 40px;
    border: 0;
    border-radius: 50%;
    background-color: transparent;
    cursor: pointer;
}
</style>