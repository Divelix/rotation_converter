<script setup lang="ts">
import { provide, ref, shallowRef } from 'vue'
import { useDark } from '@vueuse/core'
import { Axes, ToastType } from './types'
import Vis3D from './components/Vis3D.vue'
import VisControls from './components/VisControls.vue'
import VisMat from './components/VisMat.vue'
import VisQuat from './components/VisQuat.vue'
import Help from './components/Help.vue'
import Links from './components/Links.vue'
import ColorMode from './components/ColorMode.vue'

provide("isLocal", shallowRef(true))
provide("isEdit", shallowRef(false))
provide("needReset", shallowRef(false))
provide("isAlt", shallowRef(false))
provide("toastMsg", shallowRef("Toast default text"))
provide("toastType", shallowRef(ToastType.INFO))
provide("isMatApply", shallowRef(false))
provide("isQuatApply", shallowRef(false))
provide("axisCounters", ref([0, 0, 0]))
provide("rotMat", ref(new Array(9).fill(0)))
provide("quat", ref(new Array(4).fill(0)))
provide("currAxis", ref(Axes.X))
provide("snapDenom", shallowRef(6))
provide("isDark", useDark())

</script>

<template>
  <header>
    <h1>Rotation Converter</h1>
    <Help />
    <Links />
    <ColorMode />
  </header>

  <main>
    <div class="vis">
      <Vis3D />
      <VisControls />
    </div>
    <div class="repr">
      <VisMat />
      <VisQuat />
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
  justify-content: space-evenly;
  align-items: center;
  gap: 15px;
}

.vis {
  padding-left: 55px;
  display: flex;
  place-content: center;
  gap: 1em;
}

.snap {
  display: flex;
  place-content: center;
  align-items: center;
}

.repr {
  display: flex;
  flex-direction: column;
  gap: 20px;
}

@media (min-width: 1280px) {
  main {
    flex-direction: row;
    align-items: center;
    justify-content: space-evenly;
  }

  .vis {
    padding-left: 0px;
  }

  .repr {
    width: auto;
    flex-direction: column;
    align-items: center;
  }
}
</style>