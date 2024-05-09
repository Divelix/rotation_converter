<script setup lang="ts">
import { type ShallowRef, inject } from 'vue';
import IconPi from './icons/IconPi.vue';
import * as Constants from '@/const';
const snapDenom: ShallowRef<number> = inject("snapDenom")!
const isAlt: ShallowRef<Boolean> = inject("isAlt")!

const plus = () => decSnapDenom()
const minus = () => incSnapDenom()

function incSnapDenom() {
    snapDenom.value += snapDenom.value < Constants.MAX_SNAP_DENOM ? 1 : 0
}

function decSnapDenom() {
    snapDenom.value -= snapDenom.value > Constants.MIN_SNAP_DENOM ? 1 : 0
}
</script>

<template>
    <div class="label">
        <span>Snap size: </span>
    </div>
    <div class="frac">
        <div class="pi">
            <IconPi :class="{ active: isAlt }" />
        </div>
        <hr :class="{ active_hr: isAlt }">
        <div class="denom" :class="{ active: isAlt }">
            {{ snapDenom }}
        </div>
    </div>
    <div class="buttons">
        <button class="plus" @click="plus">+</button>
        <button class="minus" @click="minus">-</button>
    </div>
</template>

<style scoped>
.frac {
    display: flex;
    flex-direction: column;
    align-items: center;
    line-height: 1;
    gap: 0.2em;
    user-select: none;
}

.label {
    user-select: none;
}

.pi {
    width: 15px;
    height: 15px;
}

hr {
    width: 100%;
    border: 1px solid var(--c-text-main);
}

.active_hr {
    border-color: var(--c-text-accent);
}

.active {
    color: var(--c-text-accent);
}

.buttons {
    display: flex;
    flex-direction: column;
}

button {
    font-size: 12pt;
    border-width: 0px;
    cursor: pointer;
    color: var(--c-text-main);
    border: 1px solid var(--c-btn-border);
    background-color: var(--c-btn-bg);
}

button:hover {
    background-color: var(--c-btn-bg-hover);
}

button:active {
    background-color: var(--c-btn-bg-active);
}

.plus {
    border-radius: 50% 50% 0 0;
}

.minus {
    border-radius: 0 0 50% 50%;
}
</style>