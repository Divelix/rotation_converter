<script setup lang="ts">
import { provide, ref, shallowRef } from 'vue'
import { Axes } from './types'
import VisView from './components/VisView.vue'
import VisControls from './components/VisControls.vue'
import RotMat from './components/RotMat.vue'

provide("isLocal", shallowRef(true))
provide("axisCounters", ref([0, 0, 0]))
provide("rotMat", ref(new Array(9).fill(0)))
provide("currAxis", ref(Axes.X))
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
    <RotMat />
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
./types