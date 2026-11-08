<script setup>
import { onBeforeUnmount, onMounted, ref } from "vue"
import * as THREE from "three"
import { OrbitControls } from "three/addons/controls/OrbitControls.js"

const viewerContainer = ref(null)

let scene
let camera
let renderer
let controls
let controllerGroup
let animationFrameId
let resizeObserver

function createController() {
  controllerGroup = new THREE.Group()

  const purpleMaterial = new THREE.MeshStandardMaterial({
    color: "#6757ff",
    roughness: 0.32,
    metalness: 0.12
  })

  const darkMaterial = new THREE.MeshStandardMaterial({
    color: "#17171f",
    roughness: 0.65,
    metalness: 0.05
  })

  const bodyGeometry = new THREE.BoxGeometry(3.5, 1.65, 0.55)

  const body = new THREE.Mesh(
    bodyGeometry,
    purpleMaterial
  )

  body.geometry.translate(0, 0, 0)
  controllerGroup.add(body)

  const leftHandle = createHandle(
    -1.25,
    -0.85,
    purpleMaterial
  )

  const rightHandle = createHandle(
    1.25,
    -0.85,
    purpleMaterial
  )

  controllerGroup.add(leftHandle, rightHandle)

  const touchpad = new THREE.Mesh(
    new THREE.BoxGeometry(1.05, 0.48, 0.12),
    darkMaterial
  )

  touchpad.position.set(0, 0.3, 0.35)
  controllerGroup.add(touchpad)

  const leftAnalog = createAnalogStick(
    -0.58,
    -0.28,
    darkMaterial
  )

  const rightAnalog = createAnalogStick(
    0.58,
    -0.28,
    darkMaterial
  )

  controllerGroup.add(leftAnalog, rightAnalog)

  const directionalPad = createDirectionalPad(
    darkMaterial
  )

  directionalPad.position.set(-1.15, 0.25, 0.37)
  controllerGroup.add(directionalPad)

  const actionButtons = createActionButtons()
  actionButtons.position.set(1.15, 0.25, 0.38)
  controllerGroup.add(actionButtons)

  controllerGroup.rotation.x = -0.14
  controllerGroup.rotation.y = -0.22

  scene.add(controllerGroup)
}

function createHandle(x, y, material) {
  const geometry = new THREE.CapsuleGeometry(
    0.52,
    1.1,
    10,
    24
  )

  const handle = new THREE.Mesh(geometry, material)

  handle.position.set(x, y, -0.03)
  handle.rotation.z = x < 0 ? -0.38 : 0.38
  handle.scale.set(0.9, 1, 0.55)

  return handle
}

function createAnalogStick(x, y, material) {
  const analogGroup = new THREE.Group()

  const base = new THREE.Mesh(
    new THREE.CylinderGeometry(
      0.28,
      0.28,
      0.12,
      32
    ),
    material
  )

  base.rotation.x = Math.PI / 2

  const top = new THREE.Mesh(
    new THREE.CylinderGeometry(
      0.19,
      0.22,
      0.16,
      32
    ),
    material
  )

  top.rotation.x = Math.PI / 2
  top.position.z = 0.1

  analogGroup.add(base, top)
  analogGroup.position.set(x, y, 0.38)

  return analogGroup
}

function createDirectionalPad(material) {
  const directionalGroup = new THREE.Group()

  const horizontalPart = new THREE.Mesh(
    new THREE.BoxGeometry(0.68, 0.23, 0.12),
    material
  )

  const verticalPart = new THREE.Mesh(
    new THREE.BoxGeometry(0.23, 0.68, 0.12),
    material
  )

  directionalGroup.add(
    horizontalPart,
    verticalPart
  )

  return directionalGroup
}

function createActionButton(x, y, color) {
  const buttonMaterial = new THREE.MeshStandardMaterial({
    color,
    roughness: 0.35,
    metalness: 0.08
  })

  const button = new THREE.Mesh(
    new THREE.CylinderGeometry(
      0.13,
      0.13,
      0.1,
      24
    ),
    buttonMaterial
  )

  button.rotation.x = Math.PI / 2
  button.position.set(x, y, 0)

  return button
}

function createActionButtons() {
  const buttonGroup = new THREE.Group()

  const topButton = createActionButton(
    0,
    0.26,
    "#c17cff"
  )

  const rightButton = createActionButton(
    0.26,
    0,
    "#ff5e7d"
  )

  const bottomButton = createActionButton(
    0,
    -0.26,
    "#60e6ac"
  )

  const leftButton = createActionButton(
    -0.26,
    0,
    "#55caff"
  )

  buttonGroup.add(
    topButton,
    rightButton,
    bottomButton,
    leftButton
  )

  return buttonGroup
}

function addLights() {
  const ambientLight = new THREE.AmbientLight(
    "#ffffff",
    1.6
  )

  scene.add(ambientLight)

  const mainLight = new THREE.DirectionalLight(
    "#ffffff",
    3
  )

  mainLight.position.set(3, 4, 5)
  scene.add(mainLight)

  const purpleLight = new THREE.PointLight(
    "#6757ff",
    35,
    12
  )

  purpleLight.position.set(-3, 1, 3)
  scene.add(purpleLight)

  const blueLight = new THREE.PointLight(
    "#00bfff",
    28,
    10
  )

  blueLight.position.set(3, -2, 3)
  scene.add(blueLight)
}

function resizeScene() {
  const container = viewerContainer.value

  if (!container || !camera || !renderer) {
    return
  }

  const width = container.clientWidth
  const height = container.clientHeight

  camera.aspect = width / height
  camera.updateProjectionMatrix()

  renderer.setSize(width, height)
}

function animate() {
  animationFrameId = requestAnimationFrame(animate)

  if (controllerGroup && !controls?.dragging) {
    controllerGroup.rotation.y += 0.002
  }

  controls?.update()
  renderer?.render(scene, camera)  
}

function disposeScene() {
  scene?.traverse((object) => {
    if (object.geometry) {
      object.geometry.dispose()
    }

    if (object.material) {
      const materials = Array.isArray(object.material)
        ? object.material
        : [object.material]

      materials.forEach((material) => {
        material.dispose()
      })
    }
  })

  controls?.dispose()
  renderer?.dispose()
  renderer?.domElement.remove()
}

onMounted(() => {
  const container = viewerContainer.value

  if (!container) {
    return
  }

  scene = new THREE.Scene()

  camera = new THREE.PerspectiveCamera(
    42,
    container.clientWidth / container.clientHeight,
    0.1,
    100
  )

  camera.position.set(0, 0.4, 6.8)

  renderer = new THREE.WebGLRenderer({
    antialias: true,
    alpha: true
  })

  renderer.setPixelRatio(
    Math.min(window.devicePixelRatio, 2)
  )

  renderer.setSize(
    container.clientWidth,
    container.clientHeight
  )

  renderer.outputColorSpace = THREE.SRGBColorSpace
  renderer.setClearColor(0x000000, 0)

  container.appendChild(renderer.domElement)

  controls = new OrbitControls(
    camera,
    renderer.domElement
  )

  controls.enableDamping = true
  controls.dampingFactor = 0.06
  controls.enablePan = false

  controls.minDistance = 4.5
  controls.maxDistance = 9

  controls.minPolarAngle = Math.PI * 0.28
  controls.maxPolarAngle = Math.PI * 0.72

  controls.target.set(0, -0.1, 0)
  controls.update()

  addLights()
  createController()

  resizeObserver = new ResizeObserver(resizeScene)
  resizeObserver.observe(container)

  animate()
})

onBeforeUnmount(() => {
  cancelAnimationFrame(animationFrameId)
  resizeObserver?.disconnect()
  disposeScene()
})
</script>

<template>
  <div class="viewer-wrapper">
    <div
      ref="viewerContainer"
      class="viewer-container"
      aria-label="Visualização tridimensional de um controle"
    ></div>

    <div class="viewer-instructions">
      <span>Arraste para girar</span>
      <span>Use o scroll para aproximar</span>
    </div>
  </div>
</template>

<style scoped>
.viewer-wrapper {
  position: relative;

  width: 100%;
  min-height: 540px;

  overflow: hidden;
  border: 1px solid rgb(255 255 255 / 9%);
  border-radius: 35px;

  background:
    radial-gradient(
      circle at 50% 45%,
      rgb(102 80 255 / 22%),
      transparent 48%
    ),
    rgb(255 255 255 / 3%);
}

.viewer-wrapper::before {
  position: absolute;
  bottom: 12%;
  left: 50%;

  width: 55%;
  height: 12%;

  border-radius: 50%;
  background-color: rgb(82 62 255 / 30%);

  content: "";
  filter: blur(38px);
  pointer-events: none;
  transform: translateX(-50%);
}

.viewer-container {
  position: absolute;
  inset: 0;

  width: 100%;
  height: 100%;

  cursor: grab;
  touch-action: none;
}

.viewer-container:active {
  cursor: grabbing;
}

.viewer-container :deep(canvas) {
  display: block;
  width: 100%;
  height: 100%;
}

.viewer-instructions {
  position: absolute;
  right: 24px;
  bottom: 20px;
  left: 24px;

  display: flex;
  justify-content: space-between;
  gap: 20px;

  color: #858591;
  font-size: 0.72rem;

  pointer-events: none;
}

@media (max-width: 600px) {
  .viewer-wrapper {
    min-height: 430px;
  }

  .viewer-instructions {
    align-items: center;
    flex-direction: column;
    gap: 5px;
  }
}
</style>