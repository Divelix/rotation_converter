<script setup lang="ts">
import { Axes } from '../types'
import * as Constants from '@/const';
import { BufferGeometry, CylinderGeometry, Group, Line, LineBasicMaterial, Matrix3, Matrix4, Mesh, MeshBasicMaterial, PerspectiveCamera, Quaternion, Scene, Vector3, WebGLRenderer, type ColorRepresentation } from 'three'
import { onMounted, ref, inject, watch, type Ref, type ShallowRef, computed, type WritableComputedRef, shallowRef } from 'vue'
import { OrbitControls } from 'three/examples/jsm/Addons.js';
import Snap from './Snap.vue'

// Constants
const [W, H] = [500, 500]
const CAM_POS = new Vector3(2, 2, 2)
const CAM_LOOKAT = new Vector3(0, 0, 0)
const AXIS_LEN = 2
const CYL_RADIUS = 0.05
const CYL_LENGTH = 1
const CYL_RESOLUTION = 10
const DARK_COLOR = 0x222222
const LIGHT_COLOR = 0xd8d8d8

// Reactive stuff
const isEdit: ShallowRef<Boolean> = inject("isEdit")!
const isLocal: ShallowRef<Boolean> = inject("isLocal")!
const needReset: ShallowRef<Boolean> = inject("needReset")!
const snapDenom: ShallowRef<number> = inject("snapDenom")!
const isAlt: ShallowRef<Boolean> = inject("isAlt")!
const isMatApply: ShallowRef<Boolean> = inject("isMatApply")!
const isQuatApply: ShallowRef<Boolean> = inject("isQuatApply")!
const axisCounters: Ref<number[]> = inject("axisCounters")!
const rotMat: Ref<number[]> = inject("rotMat")!
const quat: Ref<number[]> = inject("quat")!
const currAxis: Ref<Axes> = inject("currAxis")!
const isDark: WritableComputedRef<Boolean> = inject("isDark")!
const canvasBgColor = computed(() => isDark.value ? DARK_COLOR : LIGHT_COLOR)
const canvas = ref<HTMLCanvasElement | null>(null)
const snapSize = computed(() => Math.PI / snapDenom.value)

// Variables
let controls: OrbitControls
let renderer: WebGLRenderer

// Watchers
watch(axisCounters, (newCounterValues) => {
  if (newCounterValues[0] > 0) {
    plusX()
  } else if (newCounterValues[0] < 0) {
    minusX()
  }
  if (newCounterValues[1] > 0) {
    plusY()
  } else if (newCounterValues[1] < 0) {
    minusY()
  }
  if (newCounterValues[2] > 0) {
    plusZ()
  } else if (newCounterValues[2] < 0) {
    minusZ()
  }
}, { deep: true })

watch(needReset, (newReset) => {
  if (!newReset) return
  reset()
  needReset.value = false
})

watch(isDark, (newIsDark) => {
  renderer.setClearColor(canvasBgColor.value)
})

// Methods
function snapRotate(axis: Axes, rads: number) {
  currAxis.value = axis
  if (isLocal.value) {
    switch (axis) {
      case Axes.X:
        rotFrame.rotateX(rads)
        break
      case Axes.Y:
        rotFrame.rotateY(rads)
        break
      case Axes.Z:
        rotFrame.rotateZ(rads)
        break
    }
  } else {
    const rm = new Matrix4()
    switch (axis) {
      case Axes.X:
        rm.makeRotationX(rads)
        break
      case Axes.Y:
        rm.makeRotationY(rads)
        break
      case Axes.Z:
        rm.makeRotationZ(rads)
        break
    }
    rotFrame.applyMatrix4(rm)
  }
  updateRot()
}
const minusX = () => snapRotate(Axes.X, -snapSize.value)
const plusX = () => snapRotate(Axes.X, snapSize.value)
const minusY = () => snapRotate(Axes.Y, -snapSize.value)
const plusY = () => snapRotate(Axes.Y, snapSize.value)
const minusZ = () => snapRotate(Axes.Z, -snapSize.value)
const plusZ = () => snapRotate(Axes.Z, snapSize.value)

function resetCamera() {
  camera.position.x = CAM_POS.x
  camera.position.y = CAM_POS.y
  camera.position.z = CAM_POS.z
  camera.lookAt(CAM_LOOKAT)
}

function updateRot() {
  rotFrame.updateMatrix()
  rotMat.value = new Matrix3().setFromMatrix4(rotFrame.matrix).elements
  quat.value = rotFrame.quaternion.toArray()
}

watch(isMatApply, (newMatApply) => {
  if (!newMatApply) return
  const mat3 = new Matrix3().fromArray(rotMat.value)
  const mat4 = new Matrix4().setFromMatrix3(mat3)
  rotFrame.setRotationFromMatrix(mat4)
  updateRot()
  isMatApply.value = false
})

watch(isQuatApply, (newQuatApply) => {
  if (!newQuatApply) return
  rotFrame.setRotationFromQuaternion(new Quaternion().fromArray(quat.value))
  updateRot()
  isQuatApply.value = false
})

function reset() {
  rotFrame.setRotationFromMatrix(new Matrix4().identity())
  updateRot()
  resetCamera()
}

function focusX() {
  currAxis.value = Axes.X
}
function focusY() {
  currAxis.value = Axes.Y
}
function focusZ() {
  currAxis.value = Axes.Z
}

function incCurrAxisRot() {
  switch (currAxis.value) {
    case Axes.X:
      plusX()
      break
    case Axes.Y:
      plusY()
      break
    case Axes.Z:
      plusZ()
      break
  }
}

function decCurrAxisRot() {
  switch (currAxis.value) {
    case Axes.X:
      minusX()
      break
    case Axes.Y:
      minusY()
      break
    case Axes.Z:
      minusZ()
      break
  }
}

function incSnapDenom() {
  snapDenom.value += snapDenom.value < Constants.MAX_SNAP_DENOM ? 1 : 0
}

function decSnapDenom() {
  snapDenom.value -= snapDenom.value > Constants.MIN_SNAP_DENOM ? 1 : 0
}

function handleScroll(event: WheelEvent) {
  if (isEdit.value) {
    return
  }
  event.preventDefault()
  const isUp = event.deltaY > 0
  if (isUp) {
    if (isAlt.value) {
      incSnapDenom()
    } else {
      decCurrAxisRot()
    }
  } else {
    if (isAlt.value) {
      decSnapDenom()
    } else {
      incCurrAxisRot()
    }
  }
}

function handleBlur() {
  isAlt.value = false
}

function onKeydown(event: KeyboardEvent) {
  if (isEdit.value) {
    return
  }
  event.preventDefault()
  switch (event.code) {
    case "AltLeft":
      isAlt.value = true
      break
    case "KeyC":
    case "KeyR":
    case "Space":
      reset()
      break
    case "KeyX":
    case "KeyS":
    case "Digit1":
      focusX()
      break
    case "KeyY":
    case "KeyD":
    case "Digit2":
      focusY()
      break
    case "KeyZ":
    case "KeyF":
    case "Digit3":
      focusZ()
      break
    case "ArrowUp":
    case "KeyK":
      decSnapDenom()
      break
    case "ArrowDown":
    case "KeyJ":
      incSnapDenom()
      break
    case "ArrowLeft":
    case "KeyH":
      incCurrAxisRot()
      break
    case "ArrowRight":
    case "KeyL":
      decCurrAxisRot()
      break
    case "KeyA":
    case "KeyG":
      isLocal.value = !isLocal.value
      break
    case "ShiftLeft":
      focusY()
      break
    case "ControlLeft":
      focusZ()
      break
    case "KeyV":
      renderer.setSize(300, 300)
      break
  }
}

function onKeyup(event: KeyboardEvent) {
  event.preventDefault()
  switch (event.code) {
    case "AltLeft":
      isAlt.value = false
      break
    case "ShiftLeft":
      focusX()
      break
    case "ControlLeft":
      focusX()
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
resetCamera()
scene.add(camera)

// Axes
const xLine = new Line(
  new BufferGeometry().setFromPoints([
    new Vector3(0, 0, 0),
    new Vector3(AXIS_LEN, 0, 0),
  ]),
  new LineBasicMaterial({ color: 0xff0000 })
)

const yLine = new Line(
  new BufferGeometry().setFromPoints([
    new Vector3(0, 0, 0),
    new Vector3(0, 2, 0),
  ]),
  new LineBasicMaterial({ color: 0x00ff00 })
)

const zLine = new Line(
  new BufferGeometry().setFromPoints([
    new Vector3(0, 0, 0),
    new Vector3(0, 0, 2),
  ]),
  new LineBasicMaterial({ color: 0x0000ff })
)
staticFrame.add(xLine, yLine, zLine)
scene.add(staticFrame)

// Frame
const xCyl = new Mesh(
  new CylinderGeometry(CYL_RADIUS, CYL_RADIUS, CYL_LENGTH, CYL_RESOLUTION),
  new MeshBasicMaterial({ color: 0xff0000 })
)
xCyl.rotation.z = Math.PI / 2
xCyl.position.x = CYL_LENGTH / 2

const yCyl = new Mesh(
  new CylinderGeometry(CYL_RADIUS, CYL_RADIUS, CYL_LENGTH, CYL_RESOLUTION),
  new MeshBasicMaterial({ color: 0x00ff00 })
)
yCyl.rotation.x = 0
yCyl.position.y = CYL_LENGTH / 2

const zCyl = new Mesh(
  new CylinderGeometry(CYL_RADIUS, CYL_RADIUS, CYL_LENGTH, CYL_RESOLUTION),
  new MeshBasicMaterial({ color: 0x0000ff })
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
    canvas: canvas.value as unknown as HTMLCanvasElement,
    antialias: true,
  })
  renderer.setClearColor(canvasBgColor.value)
  // renderer.setSize(W, H)
  renderer.render(scene, camera)
  controls = new OrbitControls(camera, renderer.domElement)
  controls.enablePan = false
  controls.enableZoom = false
  canvas.value?.addEventListener('wheel', handleScroll)
  window.addEventListener("keydown", onKeydown)
  window.addEventListener("keyup", onKeyup)
  window.addEventListener("blur", handleBlur)
  reset()
  loop()
})
</script>

<template>
  <div class="container">
    <canvas width="500" height="500" ref="canvas"></canvas>
    <div class="snap">
      <Snap />
    </div>
  </div>
</template>

<style scoped>
.container {
  position: relative;
}

.snap {
  position: absolute;
  top: 20px;
  right: 20px;
  display: flex;
  place-content: center;
  align-items: center;
  gap: 0.5em;
}

span {
  font-size: 20pt;
}

.coord {
  font-size: 30pt;
  width: 50px;
  height: 50px;
  margin: 10px;
}

.sign {
  font-size: 20pt;
  width: 30px;
  height: 30px;
  margin: 10px;
}

.x {
  color: var(--c-x)
}

.y {
  color: var(--c-y)
}

.z {
  color: var(--c-z)
}

canvas {
  width: 500px;
  height: 500px;
  border-radius: var(--cont-border-radius);
}
</style>
