<template>
  <canvas ref="experience"></canvas>
</template>


<script setup lang="ts">
import { BufferGeometry, CylinderGeometry, Group, Line, LineBasicMaterial, Matrix3, Matrix4, Mesh, MeshBasicMaterial, PerspectiveCamera, Scene, Vector3, WebGLRenderer } from 'three'
import { onMounted, ref, shallowRef, inject, watch, type Ref, type ShallowRef } from 'vue'
import { OrbitControls } from 'three/examples/jsm/Addons.js';


// Reactive stuff
const isLocal: ShallowRef<Boolean> = inject("isLocal")!
const axisCounters: Ref<number[]> = inject("axisCounters")!
const rotMat: Ref<number[]> = inject("rotMat")!
const experience = ref<HTMLCanvasElement | null>(null)

// Constants
enum BtnAxes { X, Y, Z }
const [W, H] = [500, 500]
const CAM_POS = new Vector3(2, 2, 2)
const CAM_LOOKAT = new Vector3(0, 0, 0)
const AXIS_LEN = 2
const CYL_RADIUS = 0.05
const CYL_LENGTH = 1
const CYL_RESOLUTION = 10
const SNAP_SIZE = Math.PI / 6

// Variables
let controls: OrbitControls
let renderer: WebGLRenderer
let currAxis: BtnAxes = BtnAxes.X

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

// Methods
function snapRotate(axis: BtnAxes, rads: number) {
  currAxis = axis
  if (isLocal.value) {
    switch (axis) {
      case BtnAxes.X:
        rotFrame.rotateX(rads)
        break
      case BtnAxes.Y:
        rotFrame.rotateY(rads)
        break
      case BtnAxes.Z:
        rotFrame.rotateZ(rads)
        break
    }
  } else {
    const rm = new Matrix4()
    switch (axis) {
      case BtnAxes.X:
        rm.makeRotationX(rads)
        break
      case BtnAxes.Y:
        rm.makeRotationY(rads)
        break
      case BtnAxes.Z:
        rm.makeRotationZ(rads)
        break
    }
    rotFrame.applyMatrix4(rm)
  }
  rotFrame.updateMatrix()
  rotMat.value = new Matrix3().setFromMatrix4(rotFrame.matrix).elements
}
const minusX = () => snapRotate(BtnAxes.X, -SNAP_SIZE)
const plusX = () => snapRotate(BtnAxes.X, SNAP_SIZE)
const minusY = () => snapRotate(BtnAxes.Y, -SNAP_SIZE)
const plusY = () => snapRotate(BtnAxes.Y, SNAP_SIZE)
const minusZ = () => snapRotate(BtnAxes.Z, -SNAP_SIZE)
const plusZ = () => snapRotate(BtnAxes.Z, SNAP_SIZE)


function resetRotation() {
  rotFrame.setRotationFromMatrix(new Matrix4().identity())
  rotFrame.updateMatrix()
  rotMat.value = new Matrix3().setFromMatrix4(rotFrame.matrix).elements
}

function focusX() {
  currAxis = BtnAxes.X
}
function focusY() {
  currAxis = BtnAxes.Y
}
function focusZ() {
  currAxis = BtnAxes.Z
}

function incCurrAxisRot() {
  switch (currAxis) {
    case BtnAxes.X:
      plusX()
      break
    case BtnAxes.Y:
      plusY()
      break
    case BtnAxes.Z:
      plusZ()
      break
  }
}

function decCurrAxisRot() {
  switch (currAxis) {
    case BtnAxes.X:
      minusX()
      break
    case BtnAxes.Y:
      minusY()
      break
    case BtnAxes.Z:
      minusZ()
      break
  }
}

function handleScroll(event: WheelEvent) {
  event.preventDefault()
  const isUp = event.deltaY > 0
  if (isUp) {
    incCurrAxisRot()
  } else {
    decCurrAxisRot()
  }
}

function onKeydown(event: KeyboardEvent) {
  event.preventDefault()
  switch (event.code) {
    case "KeyC":
    case "KeyR":
    case "Space":
      resetRotation()
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
    case "ArrowRight":
    case "KeyK":
    case "KeyL":
      incCurrAxisRot()
      break
    case "ArrowDown":
    case "ArrowLeft":
    case "KeyJ":
    case "KeyH":
      decCurrAxisRot()
      break
    case "KeyA":
    case "KeyG":
      isLocal.value = !isLocal.value
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
  window.addEventListener("keydown", onKeydown)
  loop()
})
</script>


<style scoped>
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
  color: red
}

.y {
  color: greenyellow
}

.z {
  color: blue
}
</style>
