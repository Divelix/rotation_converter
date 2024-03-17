<template>
  <span style="color: red;">X</span>
  <input
    type="range"
    :min="minAngle"
    :max="maxAngle"
    :value="sliderX"
    @input="onChangeSlider(BtnAxes.X, $event)"
    ref="refSliderX"
  />
  <span>{{ sliderX }}</span>
  <br>
  <span style="color: green;">Y</span>
  <input
    type="range"
    :min="minAngle"
    :max="maxAngle"
    :value="sliderY"
    @input="onChangeSlider(BtnAxes.Y, $event)"
    ref="refSliderY"
  />
  <span>{{ sliderY }}</span>
  <br>
  <span style="color: blue;">Z</span>
  <input
    type="range"
    :min="minAngle"
    :max="maxAngle"
    :value="sliderZ"
    @input="onChangeSlider(BtnAxes.Z, $event)"
    ref="refSliderZ"
  />
  <span>{{ sliderZ }}</span>
</template>

<script setup lang="ts">
import { ref, shallowRef } from 'vue';

const minAngle = ref(-3)
const maxAngle = ref(3)
const sliderX = shallowRef(0)
const sliderY = shallowRef(0)
const sliderZ = shallowRef(0)

enum BtnAxes {X, Y, Z}

function onChangeSlider(axis: BtnAxes, event: Event) {
  let value = Number((event.target as HTMLInputElement).value)
  switch(axis) {
    case BtnAxes.X:
      sliderX.value = value
      break
    case BtnAxes.Y:
      sliderY.value = value
      break
    case BtnAxes.Z:
      sliderZ.value = value
      break
  }
}
</script>

<style>
input[type="range"] {
  width: 450px;
  margin-left: 10px;
  margin-right: 10px;
  margin-bottom: 20px;
}
</style>