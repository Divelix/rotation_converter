<script setup lang="ts">
import { type ShallowRef, shallowRef, inject } from 'vue';
import IconPi from './icons/IconPi.vue';
const snapDenom = inject("snapDenom")
const isAlt = inject("isAlt")
</script>

<template>
    <div>
        <span>Snap: </span>
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
</template>

<style scoped>
.frac {
    display: flex;
    flex-direction: column;
    align-items: center;
    line-height: 1;
    gap: 0.2em;
}

.pi {
    width: 15px;
    height: 15px;
    /* margin-bottom: 7px; */
}

hr {
    width: 100%;
    border: 1px solid var(--c-text-dark-2);
}

.active_hr {
    border-color: white;
}

.active {
    color: white;
}
</style>