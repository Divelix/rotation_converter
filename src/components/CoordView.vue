<script setup lang="ts">
import {BufferGeometry, CylinderGeometry, Line, LineBasicMaterial, Mesh, MeshBasicMaterial, PerspectiveCamera, Scene, Vector3, WebGLRenderer} from 'three'
import {onMounted, ref} from 'vue'
import { OrbitControls } from 'three/examples/jsm/Addons.js';

// Constants
const [W, H] = [500, 500]
const axis_length = 2
const cyl_radius = 0.05
const cyl_length = 1

let controls: OrbitControls
let renderer: WebGLRenderer
let camera: PerspectiveCamera

const experience = ref<HTMLCanvasElement | null>(null)
const scene = new Scene()
camera = new PerspectiveCamera(
  45,
  W / H, 
  0.1, 
  1000
)
camera.position.x = 3;
camera.position.y = 3;
camera.position.z = 3;
camera.lookAt(new Vector3(0, 0, 0))
scene.add(camera)

// Axes
const x_line = new Line(
  new BufferGeometry().setFromPoints([
    new Vector3(0, 0, 0),
    new Vector3(axis_length, 0, 0),
  ]),
  new LineBasicMaterial({color: 0xff0000})
)
scene.add(x_line)

const y_line = new Line(
  new BufferGeometry().setFromPoints([
    new Vector3(0, 0, 0),
    new Vector3(0, 2, 0),
  ]),
  new LineBasicMaterial({color: 0x00ff00})
)
scene.add(y_line)

const z_line = new Line(
  new BufferGeometry().setFromPoints([
    new Vector3(0, 0, 0),
    new Vector3(0, 0, 2),
  ]),
  new LineBasicMaterial({color: 0x0000ff})
)
scene.add(z_line)

// Frame
const x_cyl = new Mesh(
  new CylinderGeometry(cyl_radius, cyl_radius, cyl_length, 3),
  new MeshBasicMaterial({color: 0xff0000})
)
x_cyl.rotation.z = Math.PI / 2
x_cyl.position.x = cyl_length / 2
scene.add(x_cyl)

const y_cyl = new Mesh(
  new CylinderGeometry(cyl_radius, cyl_radius, cyl_length, 3),
  new MeshBasicMaterial({color: 0x00ff00})
)
y_cyl.rotation.x = 0
y_cyl.position.y = cyl_length / 2
scene.add(y_cyl)

const z_cyl = new Mesh(
  new CylinderGeometry(cyl_radius, cyl_radius, cyl_length, 3),
  new MeshBasicMaterial({color: 0x0000ff})
)
z_cyl.rotation.x = Math.PI / 2
z_cyl.position.z = cyl_length / 2
scene.add(z_cyl)

let scrollValue: number = 0
function handleScroll(event: any) {
  event.preventDefault()
  if (event.deltaY < 0) {
    scrollValue += 0.1
  } else if (event.deltaY > 0) {
    scrollValue -= 0.1
  }
  x_cyl.rotation.z = scrollValue
}

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
  // window.addEventListener('scroll', handleScroll)
  loop()
})
</script>

<template>
  <p>Scroll: {{ scrollValue }}</p>
    <canvas ref="experience"></canvas>
</template>

<style scoped>
canvas {
  background-color: coral;
}
/* h1 {
  font-weight: 500;
  font-size: 2.6rem;
  position: relative;
  top: -10px;
}

h3 {
  font-size: 1.2rem;
}

.greetings h1,
.greetings h3 {
  text-align: center;
}

@media (min-width: 1024px) {
  .greetings h1,
  .greetings h3 {
    text-align: left;
  }
} */
</style>
