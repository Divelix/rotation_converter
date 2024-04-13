<script setup lang="ts">
import { provide, ref, shallowRef } from 'vue'
import { Axes, ToastType } from './types'
import VisView from './components/VisView.vue'
import VisControls from './components/VisControls.vue'
import RotMat from './components/RotMat.vue'

provide("isLocal", shallowRef(true))
provide("isEdit", shallowRef(false))
provide("needReset", shallowRef(false))
provide("toastMsg", shallowRef("Toast default text"))
provide("toastType", shallowRef(ToastType.INFO))
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
    <div class="rot">
      <RotMat />
    </div>
  </main>
</template>

<style scoped>
header {
  text-align: center;
  line-height: 3;
}

main {
  display: flex;
  flex-direction: column;
  gap: 1em
}

.vis {
  display: flex;
  place-content: center;
  gap: 1em;
}
.rot {
  display: flex;
  place-content: center;
}

@media (min-width: 1024px) {
  main {
    flex-direction: row;
    align-items: center;
    justify-content: space-evenly;
  }
}
</style>