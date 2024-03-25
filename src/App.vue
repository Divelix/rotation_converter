<script setup lang="ts">
import VisView from './components/VisView.vue';
import VisControls from './components/VisControls.vue'
import { provide, ref, shallowRef } from 'vue';

provide("axisCounters", ref([0, 0, 0]))
provide("isLocal", shallowRef(true))
</script>

<template>
  <header>
    <h1>Rotation Converter</h1>
  </header>

  <main>
    <div class="vis">
      <VisView />
      <VisControls />
    </div>
    <!-- <RotMat /> -->
  </main>
</template>

<style scoped>
header {
  line-height: 2.5;
}

h1 {
  text-align: center;
}

.vis {
  display: flex;
  gap: 1em;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  /* header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  } */
}
</style>
