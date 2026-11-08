<script setup>
import { onBeforeUnmount, onMounted, ref } from "vue"
import gsap from "gsap"
import { ScrollTrigger } from "gsap/ScrollTrigger"
import ControllerViewer from "./ControllerViewer.vue"

defineProps({
  selectedColor: {
    type: Object,
    required: true
  },

  colors: {
    type: Array,
    required: true
  }
})

const emit = defineEmits(["select-color"])

function selectColor(color) {
  emit("select-color", color)
}

gsap.registerPlugin(ScrollTrigger)

const productSection = ref(null)

let animationContext

onMounted(() => {
  animationContext = gsap.context(() => {
    gsap.from(".product-content > *", {
      y: 70,
      opacity: 0,
      stagger: 0.15,
      ease: "none",

      scrollTrigger: {
        trigger: ".product-content",
        start: "top 85%",
        end: "top 48%",
        scrub: 1
      }
    })

    gsap.from(".viewer-area", {
      x: 120,
      opacity: 0,
      scale: 0.82,
      ease: "none",

      scrollTrigger: {
        trigger: ".viewer-area",
        start: "top 90%",
        end: "top 48%",
        scrub: 1
      }
    })
  }, productSection.value)

  ScrollTrigger.refresh()
})

onBeforeUnmount(() => {
  animationContext?.revert()
})
</script>

<template>
  <section
    id="produto"
    ref="productSection"
    class="product-section"
  >
    <div class="product-content">
      <span class="section-label">
        Experiência imersiva
      </span>

      <h2>
        Observe todos os detalhes.
      </h2>

      <p>
        Clique e arraste para explorar o controle por diferentes
        ângulos. Use a roda do mouse para aproximar ou afastar.
      </p>

      <ul class="interaction-list">
        <div class="color-selector">
          <div class="color-selector-heading">
            <span>Cor selecionada</span>
              <strong>
                {{ selectedColor.name }}
              </strong>
          </div>
          <div
            class="color-options"
            aria-label="Escolha a cor do controle"
          >
            <button
              v-for="color in colors"
              :key="color.name"
              type="button"
              class="color-option"
              :class="{
                active: selectedColor.name === color.name
              }"
              :style="{
                backgroundColor: color.value
              }"
              :aria-label="`Selecionar a cor ${color.name}`"
              :aria-pressed="
                selectedColor.name === color.name
              "
              @click="selectColor(color)"
            ></button>
          </div>
        </div>

        <li>
          <span>01</span>
          Rotação interativa
        </li>

        <li>
          <span>02</span>
          Zoom controlado
        </li>

        <li>
          <span>03</span>
          Renderização em tempo real
        </li>
      </ul>
    </div>

    <div class="viewer-area">
      <ControllerViewer
        :color="selectedColor.value"
      />
    </div>
  </section>
</template>

<style scoped>
.product-section {
  display: grid;
  min-height: 100vh;
  grid-template-columns: 0.85fr 1.15fr;
  align-items: center;
  gap: 70px;

  overflow: hidden;
  padding: 120px 8%;

  background:
    radial-gradient(
      circle at 80% 50%,
      rgb(93 64 255 / 18%),
      transparent 38%
    ),
    #08080d;
}

.section-label {
  color: #9588ff;
  font-size: 0.77rem;
  font-weight: 800;
  letter-spacing: 0.18rem;
  text-transform: uppercase;
}

.product-content h2 {
  max-width: 680px;
  margin-top: 20px;

  font-size: clamp(3rem, 6vw, 6rem);
  line-height: 0.92;
  letter-spacing: -0.06em;
}

.product-content > p {
  max-width: 600px;
  margin-top: 28px;

  color: #9c9ca8;
  line-height: 1.8;
}

.interaction-list {
  display: flex;
  margin-top: 40px;
  flex-direction: column;
  gap: 14px;

  list-style: none;
}

.interaction-list li {
  display: flex;
  align-items: center;
  gap: 16px;

  color: #b4b4be;
  font-size: 0.88rem;
}

.interaction-list span {
  display: flex;
  width: 34px;
  height: 34px;
  align-items: center;
  justify-content: center;

  border: 1px solid rgb(119 102 255 / 35%);
  border-radius: 50%;

  color: #8172ff;
  font-size: 0.65rem;
  font-weight: 800;
}

.viewer-area {
  min-width: 0;
  will-change: transform, opacity;
}

@media (max-width: 980px) {
  .product-section {
    grid-template-columns: 1fr;
  }

  .viewer-area {
    margin-top: 20px;
  }
}

.color-selector {
  width: 100%;
  max-width: 420px;
  margin-top: 38px;
  padding-top: 28px;
  border-top: 1px solid rgb(255 255 255 / 8%);
}

.color-selector-heading {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 24px;
}

.color-selector-heading span {
  color: #858591;
  font-size: 0.8rem;
  white-space: nowrap;
}

.color-selector-heading strong {
  color: #ffffff;
  font-size: 0.85rem;
}

.color-options {
  display: flex;
  margin-top: 18px;
  flex-wrap: wrap;
  gap: 13px;
}

.color-option {
  position: relative;

  width: 40px;
  height: 40px;

  border: 4px solid #08080d;
  border-radius: 50%;
  outline: 1px solid rgb(255 255 255 / 18%);

  box-shadow:
    0 6px 16px rgb(0 0 0 / 30%),
    inset 0 2px 4px rgb(255 255 255 / 22%);

  transition:
    transform 0.25s ease,
    outline-color 0.25s ease,
    box-shadow 0.25s ease;
}

.color-option:hover {
  transform: translateY(-3px) scale(1.05);
}

.color-option.active {
  outline: 2px solid #ffffff;
  transform: scale(1.12);

  box-shadow:
    0 0 0 4px rgb(119 102 255 / 20%),
    0 8px 20px rgb(0 0 0 / 35%);
}

.color-option:focus-visible {
  outline: 3px solid #ffffff;
  outline-offset: 4px;
}

@media (max-width: 600px) {
  .product-section {
    gap: 45px;
    padding: 100px 24px;
  }
}
</style>