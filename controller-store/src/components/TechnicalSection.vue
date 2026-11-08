<script setup>
import {
  computed,
  onBeforeUnmount,
  onMounted,
  ref
} from "vue"

import gsap from "gsap"
import { ScrollTrigger } from "gsap/ScrollTrigger"

import ControllerViewer from "./ControllerViewer.vue"

gsap.registerPlugin(ScrollTrigger)

const props = defineProps({
  selectedColor: {
    type: Object,
    required: true
  }
})

const technicalSection = ref(null)
const activeIndex = ref(0)

let animationContext

const features = [
  {
    number: "01",
    tag: "Precisão",
    title: "Comandos em cada movimento.",
    description:
      "Analógicos e botões construídos para demonstrar respostas rápidas e controle durante diferentes estilos de jogo.",
    metric: "Resposta imediata",
    angle: -0.15
  },
  {
    number: "02",
    tag: "Ergonomia",
    title: "Conforto durante toda a partida.",
    description:
      "Empunhaduras arredondadas e posicionamento equilibrado ajudam a criar uma experiência confortável em sessões prolongadas.",
    metric: "Pegada confortável",
    angle: 0.72
  },
  {
    number: "03",
    tag: "Personalização",
    title: "Um controle com a sua identidade.",
    description:
      "A interface Vue atualiza os materiais do Three.js em tempo real, permitindo visualizar diferentes acabamentos antes da escolha.",
    metric: "5 opções de cor",
    angle: -0.82
  },
  {
    number: "04",
    tag: "Experiência 3D",
    title: "Explore por todos os ângulos.",
    description:
      "A renderização WebGL permite aproximar, girar e observar o produto sem depender de uma sequência estática de imagens.",
    metric: "360 graus",
    angle: Math.PI * 0.95
  }
]

const activeFeature = computed(() => {
  return features[activeIndex.value]
})

onMounted(() => {
  animationContext = gsap.context(() => {
    const cards = gsap.utils.toArray(
      ".technical-card"
    )

    cards.forEach((card, index) => {
      ScrollTrigger.create({
        trigger: card,
        start: "top 58%",
        end: "bottom 42%",

        onEnter: () => {
          activeIndex.value = index
        },

        onEnterBack: () => {
          activeIndex.value = index
        }
      })

      gsap.from(card, {
        y: 70,
        opacity: 0,
        ease: "none",

        scrollTrigger: {
          trigger: card,
          start: "top 88%",
          end: "top 62%",
          scrub: 1
        }
      })
    })

    gsap.from(".technical-heading > *", {
      y: 60,
      opacity: 0,
      stagger: 0.15,
      ease: "none",

      scrollTrigger: {
        trigger: ".technical-heading",
        start: "top 85%",
        end: "top 52%",
        scrub: 1
      }
    })
  }, technicalSection.value)

  ScrollTrigger.refresh()
})

onBeforeUnmount(() => {
  animationContext?.revert()
})
</script>

<template>
  <section
    ref="technicalSection"
    class="technical-section"
  >
    <header class="technical-heading">
      <span>Construído em detalhes</span>

      <h2>
        Uma experiência que evolui durante o scroll.
      </h2>

      <p>
        Percorra as características e observe como
        cada etapa modifica a apresentação do produto.
      </p>
    </header>

    <div class="technical-layout">
      <div class="sticky-column">
        <div class="active-information">
          <span class="active-number">
            {{ activeFeature.number }}
          </span>

          <div>
            <span class="active-tag">
              {{ activeFeature.tag }}
            </span>

            <strong>
              {{ activeFeature.metric }}
            </strong>
          </div>
        </div>

        <ControllerViewer
          :color="selectedColor.value"
          :auto-rotate="false"
          :presentation-angle="activeFeature.angle"
          :show-instructions="false"
        />

        <div class="progress-navigation">
          <span
            v-for="(feature, index) in features"
            :key="feature.number"
            :class="{
              active: activeIndex === index
            }"
          ></span>
        </div>
      </div>

      <div class="technical-cards">
        <article
          v-for="(feature, index) in features"
          :key="feature.number"
          class="technical-card"
          :class="{
            active: activeIndex === index
          }"
        >
          <span class="card-number">
            {{ feature.number }}
          </span>

          <div>
            <span class="card-tag">
              {{ feature.tag }}
            </span>

            <h3>{{ feature.title }}</h3>

            <p>{{ feature.description }}</p>
          </div>
        </article>
      </div>
    </div>
  </section>
</template>

<style scoped>
.technical-section {
  position: relative;
  padding: 130px 7% 170px;

  background:
    radial-gradient(
      circle at 20% 35%,
      rgb(86 63 255 / 17%),
      transparent 30%
    ),
    #07070c;
}

.technical-heading {
  max-width: 950px;
  margin-bottom: 100px;
}

.technical-heading > span {
  color: #9588ff;

  font-size: 0.76rem;
  font-weight: 800;
  letter-spacing: 0.18rem;
  text-transform: uppercase;
}

.technical-heading h2 {
  max-width: 900px;
  margin-top: 20px;

  font-size: clamp(3rem, 6vw, 6.2rem);
  line-height: 0.92;
  letter-spacing: -0.065em;
}

.technical-heading p {
  max-width: 620px;
  margin-top: 28px;

  color: #9999a5;
  line-height: 1.8;
}

.technical-layout {
  display: grid;
  grid-template-columns:
    minmax(0, 1.15fr)
    minmax(360px, 0.85fr);
  align-items: start;
  gap: 80px;
}

.sticky-column {
  position: sticky;
  top: 105px;
}

.sticky-column :deep(.viewer-wrapper) {
  min-height: 620px;
}

.active-information {
  position: absolute;
  z-index: 4;
  top: 25px;
  right: 25px;
  left: 25px;

  display: flex;
  align-items: center;
  justify-content: space-between;

  pointer-events: none;
}

.active-number {
  display: flex;
  width: 46px;
  height: 46px;
  align-items: center;
  justify-content: center;

  border: 1px solid rgb(119 102 255 / 45%);
  border-radius: 50%;

  color: #9c8fff;
  font-size: 0.72rem;
  font-weight: 800;
}

.active-information > div {
  display: flex;
  align-items: flex-end;
  flex-direction: column;
  gap: 5px;
}

.active-tag {
  color: #7e7e8a;
  font-size: 0.68rem;
  letter-spacing: 0.12rem;
  text-transform: uppercase;
}

.active-information strong {
  font-size: 0.9rem;
}

.progress-navigation {
  position: absolute;
  z-index: 4;
  right: 25px;
  bottom: 25px;
  left: 25px;

  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 8px;

  pointer-events: none;
}

.progress-navigation span {
  height: 3px;
  overflow: hidden;
  border-radius: 999px;

  background: rgb(255 255 255 / 10%);

  transition:
    background-color 0.35s ease,
    transform 0.35s ease;
}

.progress-navigation span.active {
  background: #8172ff;
  transform: scaleY(1.7);
}

.technical-cards {
  display: flex;
  flex-direction: column;
  gap: 120px;
  padding: 80px 0;
}

.technical-card {
  display: flex;
  min-height: 400px;
  justify-content: space-between;
  flex-direction: column;

  padding: 38px;

  border: 1px solid rgb(255 255 255 / 8%);
  border-radius: 30px;

  background:
    linear-gradient(
      145deg,
      rgb(255 255 255 / 6%),
      rgb(255 255 255 / 2%)
    );

  opacity: 0.48;

  transition:
    opacity 0.4s ease,
    border-color 0.4s ease,
    background-color 0.4s ease,
    transform 0.4s ease;
}

.technical-card.active {
  border-color: rgb(119 102 255 / 55%);
  background-color: rgb(119 102 255 / 6%);
  opacity: 1;
  transform: translateX(-12px);
}

.card-number {
  color: #7766ff;
  font-size: 0.8rem;
  font-weight: 800;
}

.card-tag {
  color: #8c7fff;

  font-size: 0.72rem;
  font-weight: 800;
  letter-spacing: 0.13rem;
  text-transform: uppercase;
}

.technical-card h3 {
  max-width: 480px;
  margin-top: 16px;

  font-size: clamp(2rem, 3vw, 3.3rem);
  line-height: 1;
  letter-spacing: -0.045em;
}

.technical-card p {
  max-width: 500px;
  margin-top: 23px;

  color: #9b9ba6;
  line-height: 1.75;
}

@media (max-width: 1050px) {
  .technical-layout {
    grid-template-columns: 1fr;
  }

  .sticky-column {
    position: relative;
    top: auto;
  }

  .technical-cards {
    gap: 30px;
    padding-top: 20px;
  }

  .technical-card {
    min-height: 320px;
  }

  .technical-card.active {
    transform: none;
  }
}

@media (max-width: 600px) {
  .technical-section {
    padding: 100px 24px;
  }

  .technical-heading {
    margin-bottom: 60px;
  }

  .sticky-column :deep(.viewer-wrapper) {
    min-height: 430px;
  }

  .technical-card {
    min-height: 300px;
    padding: 28px;
  }

  .active-information {
    top: 18px;
    right: 18px;
    left: 18px;
  }
}
</style>