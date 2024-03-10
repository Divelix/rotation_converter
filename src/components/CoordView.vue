<template>
  <canvas ref="experience"></canvas>
  <br>
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
  <input
    type="range"
    :min="minAngle"
    :max="maxAngle"
    :value="sliderZ"
    @input="onChangeSlider(BtnAxes.Z, $event)"
    ref="refSliderZ"
  />
  <span>{{ sliderZ }}</span>
  <br>
  <button class="coord" @click="setX">x</button>
  <button class="coord" @click="setY">y</button>
  <button class="coord" @click="setZ">z</button>
  <p>
    1 2 3 <br>
    1 2 3 <br>
    1 2 3 <br>
  </p>
</template>


<script setup lang="ts">
import {BufferGeometry, CylinderGeometry, Group, Line, LineBasicMaterial, Mesh, MeshBasicMaterial, PerspectiveCamera, Scene, Vector3, WebGLRenderer} from 'three'
import {computed, onMounted, ref, shallowRef, watch} from 'vue'
import { OrbitControls } from 'three/examples/jsm/Addons.js';
import { clamp } from 'three/src/math/MathUtils.js';

// Reactive stuff
const sliderX = shallowRef(0)
const sliderY = shallowRef(0)
const sliderZ = shallowRef(0)
const sliderAsAngleX = computed(()=>Math.PI / 6 * sliderX.value)
const sliderAsAngleY = computed(()=>Math.PI / 6 * sliderY.value)
const sliderAsAngleZ = computed(()=>Math.PI / 6 * sliderZ.value)
const refSliderX = ref<HTMLInputElement | null>(null)
const refSliderY = ref<HTMLInputElement | null>(null)
const refSliderZ = ref<HTMLInputElement | null>(null)
const experience = ref<HTMLCanvasElement | null>(null)
const minAngle = ref(-3)
const maxAngle = ref(3)
const rotMat = ref()

// Constants
enum BtnAxes {X, Y, Z}
const [W, H] = [500, 500]
const CAM_POS = new Vector3(3,3,3)
const CAM_LOOKAT = new Vector3(0,0,0)
const AXIS_LEN = 2
const CYL_RADIUS = 0.05
const CYL_LENGTH = 1
const CYL_RESOLUTION = 10
const SNAP_SIZE = 1

// Variables
let controls: OrbitControls
let renderer: WebGLRenderer
let currAxis: BtnAxes = BtnAxes.X

// Methods
watch(sliderX, (newX) => {
  (refSliderX.value as unknown as HTMLInputElement).value = newX.toString()
  rotFrame.rotation.x = sliderAsAngleX.value
})
watch(sliderY, (newY) => {
  (refSliderY.value as unknown as HTMLInputElement).value = newY.toString()
  rotFrame.rotation.y = sliderAsAngleY.value
})
watch(sliderZ, (newZ) => {
  (refSliderZ.value as unknown as HTMLInputElement).value = newZ.toString()
  rotFrame.rotation.z = sliderAsAngleZ.value
})

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

function setX() {
  currAxis = BtnAxes.X
}
function setY() {
  currAxis = BtnAxes.Y
}
function setZ() {
  currAxis = BtnAxes.Z
}

function handleScroll(event: WheelEvent) {
  event.preventDefault()
  let scrollValue = event.deltaY < 0 ? SNAP_SIZE : -SNAP_SIZE
  switch(currAxis){
    case BtnAxes.X:
      sliderX.value = clamp(sliderX.value + scrollValue, minAngle.value, maxAngle.value)
      break
    case BtnAxes.Y:
      sliderY.value = clamp(sliderY.value + scrollValue, minAngle.value, maxAngle.value)
      break
    case BtnAxes.Z:
      sliderZ.value = clamp(sliderZ.value + scrollValue, minAngle.value, maxAngle.value)
      break
  }
}

// Setup scene
const scene = new Scene()
const staticFrame = new Group() // static frame
const rotFrame = new Group() // rotational frame
staticFrame.rotation.x = -Math.PI / 2
staticFrame.rotation.z = -Math.PI / 2
staticFrame.add(rotFrame)

const camera = new PerspectiveCamera(
  45,
  W / H, 
  0.1, 
  1000
)
camera.position.x = CAM_POS.x
camera.position.y = CAM_POS.y
camera.position.z = CAM_POS.z
camera.lookAt(CAM_LOOKAT)
scene.add(camera)

// Axes
const xLine = new Line(
  new BufferGeometry().setFromPoints([
    new Vector3(0, 0, 0),
    new Vector3(AXIS_LEN, 0, 0),
  ]),
  new LineBasicMaterial({color: 0xff0000})
)

const yLine = new Line(
  new BufferGeometry().setFromPoints([
    new Vector3(0, 0, 0),
    new Vector3(0, 2, 0),
  ]),
  new LineBasicMaterial({color: 0x00ff00})
)

const zLine = new Line(
  new BufferGeometry().setFromPoints([
    new Vector3(0, 0, 0),
    new Vector3(0, 0, 2),
  ]),
  new LineBasicMaterial({color: 0x0000ff})
)
staticFrame.add(xLine, yLine, zLine)
scene.add(staticFrame)

// Frame
const xCyl = new Mesh(
  new CylinderGeometry(CYL_RADIUS, CYL_RADIUS, CYL_LENGTH, CYL_RESOLUTION),
  new MeshBasicMaterial({color: 0xff0000})
)
xCyl.rotation.z = Math.PI / 2
xCyl.position.x = CYL_LENGTH / 2

const yCyl = new Mesh(
  new CylinderGeometry(CYL_RADIUS, CYL_RADIUS, CYL_LENGTH, CYL_RESOLUTION),
  new MeshBasicMaterial({color: 0x00ff00})
)
yCyl.rotation.x = 0
yCyl.position.y = CYL_LENGTH / 2

const zCyl = new Mesh(
  new CylinderGeometry(CYL_RADIUS, CYL_RADIUS, CYL_LENGTH, CYL_RESOLUTION),
  new MeshBasicMaterial({color: 0x0000ff})
)
zCyl.rotation.x = Math.PI / 2
zCyl.position.z = CYL_LENGTH / 2
rotFrame.add(xCyl, yCyl, zCyl)

const loop = () => {
  renderer.render(scene, camera)
  requestAnimationFrame(loop)
}

onMounted(() => {
  renderer = new WebGLRenderer({
      canvas: experience.value as unknown as HTMLCanvasElement,
      antialias: true,
  })
  renderer.setClearColor(0x282828)
  renderer.setSize(W, H)
  renderer.render(scene, camera)
  controls = new OrbitControls(camera, renderer.domElement)
  controls.enablePan = false
  controls.enableZoom = false
  experience.value?.addEventListener('wheel', handleScroll)
  loop()
})

</script>


<style scoped>
.coord {
  font-size: 50pt;
  width: 100px;
  height: 100px;
  margin: 10px;
}
input[type="range"] {
  width: 450px;
  margin-right: 10px;
  margin-bottom: 20px;
}
</style>
