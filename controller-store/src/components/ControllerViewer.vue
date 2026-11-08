<script setup>
import {
  onBeforeUnmount,
  onMounted,
  ref,
  watch
} from "vue"
import * as THREE from "three"
import gsap from "gsap"
import { OrbitControls } from "three/addons/controls/OrbitControls.js"
import { RoundedBoxGeometry } from "three/addons/geometries/RoundedBoxGeometry.js"
import { RoomEnvironment } from "three/addons/environments/RoomEnvironment.js"


const props = defineProps({
  color: {
    type: String,
    default: "#6757ff"
  },

  autoRotate: {
    type: Boolean,
    default: true
  },

  presentationAngle: {
    type: Number,
    default: -0.2
  },

  showInstructions: {
    type: Boolean,
    default: true
  }
})

const viewerContainer = ref(null)

let scene
let camera
let renderer
let controls
let controllerGroup
let animationFrameId
let resizeObserver
let isUserInteracting = false

let shellMaterial
let shellSideMaterial

let environmentTexture
let roomEnvironment
let pmremGenerator 






function createController() {
  controllerGroup = new THREE.Group()
  const darkerColor = new THREE.Color(props.color)
  darkerColor.multiplyScalar(0.65)

  shellMaterial = new THREE.MeshPhysicalMaterial({
    color: darkerColor,
    roughness: 0.3,
    metalness: 0.05,
    clearcoat: 0.35,
    clearcoatRoughness: 0.24,
    envMapIntensity: 1.1
  })

  shellSideMaterial = new THREE.MeshPhysicalMaterial({
    color: darkerColor,
    roughness: 0.4,
    metalness: 0.04,
    clearcoat: 0.2,
    clearcoatRoughness: 0.32,
    envMapIntensity: 0.8
  })

  const rubberMaterial = new THREE.MeshStandardMaterial({
    color: "#16161d",
    roughness: 0.72,
    metalness: 0.02,
    envMapIntensity: 0.45
  })

  const touchpadMaterial = new THREE.MeshStandardMaterial({
    color: "#202029",
    roughness: 0.58,
    metalness: 0.08,
    envMapIntensity: 0.65
  })

  const centerBody = new THREE.Mesh(
    new RoundedBoxGeometry(
      3.55,
      1.5,
      0.58,
      8,
      0.34
    ),
    shellMaterial
  )

  centerBody.position.set(0, 0.14, 0)
  centerBody.castShadow = true
  centerBody.receiveShadow = true

  controllerGroup.add(centerBody)

  const lowerBody = new THREE.Mesh(
    new RoundedBoxGeometry(
      2.75,
      0.76,
      0.62,
      8,
      0.3
    ),
    shellSideMaterial
  )

  lowerBody.position.set(0, -0.56, -0.04)
  lowerBody.castShadow = true
  lowerBody.receiveShadow = true

  controllerGroup.add(lowerBody)

  const leftHandle = createImprovedHandle(
    -1.27,
    -0.78,
    -0.36,
    shellSideMaterial
  )

  const rightHandle = createImprovedHandle(
    1.27,
    -0.78,
    0.36,
    shellSideMaterial
  )

  controllerGroup.add(leftHandle, rightHandle)

  const touchpad = new THREE.Mesh(
    new RoundedBoxGeometry(
      1.18,
      0.54,
      0.1,
      6,
      0.12
    ),
    touchpadMaterial
  )

  touchpad.position.set(0, 0.43, 0.34)
  touchpad.rotation.x = -0.05
  touchpad.castShadow = true

  controllerGroup.add(touchpad)

  const lightBar = createLightBar()
  lightBar.position.set(0, 0.83, 0.08)

  controllerGroup.add(lightBar)

  const leftAnalog = createImprovedAnalog(
    -0.54,
    -0.3,
    rubberMaterial
  )

  const rightAnalog = createImprovedAnalog(
    0.54,
    -0.3,
    rubberMaterial
  )

  controllerGroup.add(leftAnalog, rightAnalog)

  const directionalPad = createImprovedDirectionalPad(
    rubberMaterial
  )

  directionalPad.position.set(-1.17, 0.22, 0.39)
  directionalPad.rotation.z = 0.02

  controllerGroup.add(directionalPad)

  const actionButtons = createImprovedActionButtons()
  actionButtons.position.set(1.17, 0.22, 0.4)

  controllerGroup.add(actionButtons)

  const leftOptionsButton = createSmallCenterButton(
    -0.77,
    0.48,
    touchpadMaterial
  )

  const rightOptionsButton = createSmallCenterButton(
    0.77,
    0.48,
    touchpadMaterial
  )

  controllerGroup.add(
    leftOptionsButton,
    rightOptionsButton
  )

  const speaker = createSpeaker()
  speaker.position.set(0, -0.02, 0.37)

  controllerGroup.add(speaker)

  const leftTrigger = createTrigger(
    -1.05,
    0.8,
    rubberMaterial
  )

  const rightTrigger = createTrigger(
    1.05,
    0.8,
    rubberMaterial
  )

  controllerGroup.add(leftTrigger, rightTrigger)

  controllerGroup.rotation.x = -0.12
  controllerGroup.rotation.y = props.presentationAngle
  controllerGroup.position.y = 0.12

  scene.add(controllerGroup)
}

watch(
  () => props.color,

  (newColor) => {
    if (!shellMaterial || !shellSideMaterial) {
      return
    }

    shellMaterial.color.set(newColor)

    const darkerColor = new THREE.Color(newColor)
    darkerColor.multiplyScalar(0.65)

    shellSideMaterial.color.copy(darkerColor)
  }
)

watch(
  () => props.presentationAngle,

  (newAngle) => {
    if (!controllerGroup) {
      return
    }

    gsap.to(controllerGroup.rotation, {
      y: newAngle,
      duration: 1.1,
      ease: "power3.inOut",
      overwrite: true
    })
  }
)

function createImprovedHandle(
  x,
  y,
  rotation,
  material
) {
  const handleGroup = new THREE.Group()

  const handle = new THREE.Mesh(
    new THREE.CapsuleGeometry(
      0.55,
      1.22,
      14,
      32
    ),
    material
  )

  handle.scale.set(0.86, 1, 0.58)
  handle.rotation.z = rotation
  handle.castShadow = true
  handle.receiveShadow = true

  const gripMaterial = new THREE.MeshStandardMaterial({
    color: "#24242d",
    roughness: 0.86,
    metalness: 0
  })

  const grip = new THREE.Mesh(
    new THREE.CapsuleGeometry(
      0.41,
      0.98,
      12,
      24
    ),
    gripMaterial
  )

  grip.position.z = -0.2
  grip.scale.set(0.84, 0.96, 0.42)
  grip.rotation.z = rotation
  grip.castShadow = true

  handleGroup.add(handle, grip)
  handleGroup.position.set(x, y, -0.09)

  return handleGroup
}

function createImprovedAnalog(x, y, material) {
  const analogGroup = new THREE.Group()

  const outerRing = new THREE.Mesh(
    new THREE.TorusGeometry(
      0.27,
      0.055,
      16,
      40
    ),
    material
  )

  outerRing.position.z = 0.015

  const stem = new THREE.Mesh(
    new THREE.CylinderGeometry(
      0.11,
      0.14,
      0.16,
      32
    ),
    material
  )

  stem.rotation.x = Math.PI / 2
  stem.position.z = 0.09

  const top = new THREE.Mesh(
    new THREE.CylinderGeometry(
      0.2,
      0.22,
      0.1,
      40
    ),
    material
  )

  top.rotation.x = Math.PI / 2
  top.position.z = 0.2

  const topInset = new THREE.Mesh(
    new THREE.CylinderGeometry(
      0.145,
      0.155,
      0.015,
      40
    ),
    new THREE.MeshStandardMaterial({
      color: "#292932",
      roughness: 0.95
    })
  )

  topInset.rotation.x = Math.PI / 2
  topInset.position.z = 0.255

  analogGroup.add(
    outerRing,
    stem,
    top,
    topInset
  )

  analogGroup.position.set(x, y, 0.36)

  analogGroup.traverse((object) => {
    if (object.isMesh) {
      object.castShadow = true
    }
  })

  return analogGroup
}

function createImprovedDirectionalPad(material) {
  const directionalGroup = new THREE.Group()

  const horizontalPart = new THREE.Mesh(
    new RoundedBoxGeometry(
      0.72,
      0.24,
      0.11,
      4,
      0.06
    ),
    material
  )

  const verticalPart = new THREE.Mesh(
    new RoundedBoxGeometry(
      0.24,
      0.72,
      0.11,
      4,
      0.06
    ),
    material
  )

  horizontalPart.castShadow = true
  verticalPart.castShadow = true

  const center = new THREE.Mesh(
    new THREE.CylinderGeometry(
      0.13,
      0.13,
      0.12,
      24
    ),
    new THREE.MeshStandardMaterial({
      color: "#202028",
      roughness: 0.65
    })
  )

  center.rotation.x = Math.PI / 2
  center.position.z = 0.04
  center.castShadow = true

  directionalGroup.add(
    horizontalPart,
    verticalPart,
    center
  )

  return directionalGroup
}

function createImprovedActionButton(
  x,
  y,
  color
) {
  const buttonGroup = new THREE.Group()

  const baseMaterial = new THREE.MeshStandardMaterial({
    color: "#15151c",
    roughness: 0.54,
    metalness: 0.08
  })

  const buttonMaterial = new THREE.MeshPhysicalMaterial({
    color,
    roughness: 0.25,
    metalness: 0.04,
    clearcoat: 0.7,
    clearcoatRoughness: 0.15,
    emissive: color,
    emissiveIntensity: 0.035
  })

  const base = new THREE.Mesh(
    new THREE.CylinderGeometry(
      0.17,
      0.17,
      0.08,
      32
    ),
    baseMaterial
  )

  base.rotation.x = Math.PI / 2

  const button = new THREE.Mesh(
    new THREE.CylinderGeometry(
      0.125,
      0.135,
      0.1,
      32
    ),
    buttonMaterial
  )

  button.rotation.x = Math.PI / 2
  button.position.z = 0.065
  button.castShadow = true

  buttonGroup.add(base, button)
  buttonGroup.position.set(x, y, 0)

  return buttonGroup
}

function createImprovedActionButtons() {
  const buttonGroup = new THREE.Group()

  buttonGroup.add(
    createImprovedActionButton(
      0,
      0.27,
      "#c481ff"
    ),
    createImprovedActionButton(
      0.27,
      0,
      "#ff5f82"
    ),
    createImprovedActionButton(
      0,
      -0.27,
      "#5de7ac"
    ),
    createImprovedActionButton(
      -0.27,
      0,
      "#58caff"
    )
  )

  return buttonGroup
}

function createSmallCenterButton(
  x,
  y,
  material
) {
  const button = new THREE.Mesh(
    new RoundedBoxGeometry(
      0.22,
      0.1,
      0.07,
      4,
      0.035
    ),
    material
  )

  button.position.set(x, y, 0.36)
  button.castShadow = true

  return button
}

function createSpeaker() {
  const speakerGroup = new THREE.Group()

  const holeMaterial =
    new THREE.MeshStandardMaterial({
      color: "#0c0c11",
      roughness: 1
    })

  const columns = 5
  const rows = 2
  const spacing = 0.105

  for (let row = 0; row < rows; row += 1) {
    for (
      let column = 0;
      column < columns;
      column += 1
    ) {
      const hole = new THREE.Mesh(
        new THREE.CircleGeometry(0.022, 16),
        holeMaterial
      )

      hole.position.set(
        (column - (columns - 1) / 2) * spacing,
        (row - (rows - 1) / 2) * spacing,
        0
      )

      speakerGroup.add(hole)
    }
  }

  return speakerGroup
}

function createTrigger(x, y, material) {
  const trigger = new THREE.Mesh(
    new RoundedBoxGeometry(
      0.58,
      0.22,
      0.2,
      5,
      0.08
    ),
    material
  )

  trigger.position.set(x, y, -0.18)
  trigger.rotation.x = 0.14
  trigger.castShadow = true

  return trigger
}

function createLightBar() {
  const lightMaterial =
    new THREE.MeshStandardMaterial({
      color: "#96dfff",
      emissive: "#39bfff",
      emissiveIntensity: 2.2,
      roughness: 0.28
    })

  const lightBar = new THREE.Mesh(
    new RoundedBoxGeometry(
      0.9,
      0.07,
      0.06,
      4,
      0.025
    ),
    lightMaterial
  )

  return lightBar
}

function addLights() {
  const hemisphereLight =
    new THREE.HemisphereLight(
      "#dce7ff",
      "#171225",
      1.5
    )

  scene.add(hemisphereLight)

  const keyLight = new THREE.DirectionalLight(
    "#ffffff",
    3.2
  )

  keyLight.position.set(4, 5, 6)
  keyLight.castShadow = true

  keyLight.shadow.mapSize.width = 1024
  keyLight.shadow.mapSize.height = 1024

  keyLight.shadow.camera.near = 0.1
  keyLight.shadow.camera.far = 25

  keyLight.shadow.camera.left = -6
  keyLight.shadow.camera.right = 6
  keyLight.shadow.camera.top = 6
  keyLight.shadow.camera.bottom = -6

  keyLight.shadow.bias = -0.0005

  scene.add(keyLight)

  const purpleLight = new THREE.PointLight(
    "#6c56ff",
    32,
    12,
    2
  )

  purpleLight.position.set(-3.5, 1.5, 3)
  scene.add(purpleLight)

  const blueLight = new THREE.PointLight(
    "#42c8ff",
    22,
    10,
    2
  )

  blueLight.position.set(3.5, -1, 3)
  scene.add(blueLight)

  const rimLight = new THREE.DirectionalLight(
    "#917cff",
    2
  )

  rimLight.position.set(-4, 2, -3)
  scene.add(rimLight)
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

  environmentTexture?.dispose()
  roomEnvironment?.dispose()
  pmremGenerator?.dispose()

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

  camera.position.set(0, 0.25, 6.6)

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

  renderer.toneMapping = THREE.ACESFilmicToneMapping
  renderer.toneMappingExposure = 1.15

  renderer.shadowMap.enabled = true
  renderer.shadowMap.type = THREE.PCFSoftShadowMap

  pmremGenerator = new THREE.PMREMGenerator(renderer)
  pmremGenerator.compileEquirectangularShader()

  roomEnvironment = new RoomEnvironment()

  environmentTexture = pmremGenerator.fromScene(
    roomEnvironment
  ).texture

  scene.environment = environmentTexture

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

  controls.target.set(0, -0.08, 0)
  controls.update()

  addLights()
  createController()
  createShadowFloor()

  resizeObserver = new ResizeObserver(resizeScene)
  resizeObserver.observe(container)

  animate()

  resizeObserver = new ResizeObserver(resizeScene)
  resizeObserver.observe(container)

  function createShadowFloor() {
    const floorMaterial =
      new THREE.ShadowMaterial({
        color: "#000000",
        opacity: 0.32,
        transparent: true
      })

    const floor = new THREE.Mesh(
      new THREE.PlaneGeometry(10, 10),
      floorMaterial
    )

    floor.rotation.x = -Math.PI / 2
    floor.position.y = -1.62
    floor.receiveShadow = true

    scene.add(floor)
}
function animate() {
  animationFrameId = requestAnimationFrame(animate)

  if (
  controllerGroup &&
  props.autoRotate &&
  !isUserInteracting
  ) {
  controllerGroup.rotation.y += 0.0015
  }
  controls?.update()
  renderer?.render(scene, camera)
}
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

    <div
      v-if="props.showInstructions"
      class="viewer-instructions"
      >
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
    circle at 50% 42%,
    rgb(118 101 255 / 25%),
    transparent 36%
  ),
  radial-gradient(
    circle at 80% 70%,
    rgb(42 173 255 / 12%),
    transparent 30%
  ),
  linear-gradient(
    145deg,
    rgb(255 255 255 / 5%),
    rgb(255 255 255 / 1%)
  );
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