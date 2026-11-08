<script setup>
import { onBeforeUnmount, onMounted, ref } from "vue"
import gsap from "gsap"
import { ScrollTrigger } from "gsap/ScrollTrigger"

gsap.registerPlugin(ScrollTrigger)

const section = ref(null)
let animationContext

const features = [
  {
    number: "01",
    title: "Resposta rápida",
    description:
      "Comandos precisos para que cada movimento seja transmitido com agilidade."
  },
  {
    number: "02",
    title: "Conforto prolongado",
    description:
      "Formato desenvolvido para manter uma pegada confortável durante longas partidas."
  },
  {
    number: "03",
    title: "Controle completo",
    description:
      "Analógicos, gatilhos e botões posicionados para facilitar o acesso aos comandos."
  }
]

onMounted(() => {
  animationContext = gsap.context(() => {
    const headingTimeline = gsap.timeline({
      scrollTrigger: {
        trigger: ".features-heading",
        start: "top 85%",
        end: "top 45%",
        scrub: 1,
        markers: false
      }
    })

    headingTimeline
      .from(".section-label", {
        y: 40,
        opacity: 0
      })
      .from(
        ".features-heading h2",
        {
          y: 80,
          opacity: 0
        },
        "-=0.3"
      )
      .from(
        ".features-heading > p",
        {
          y: 50,
          opacity: 0
        },
        "-=0.3"
      )

    gsap.fromTo(
      ".feature-card",
      {
        y: 140,
        opacity: 0,
        scale: 0.86,
        rotationX: 18
      },
      {
        y: 0,
        opacity: 1,
        scale: 1,
        rotationX: 0,
        stagger: 0.12,
        ease: "none",

        scrollTrigger: {
          trigger: ".features-grid",
          start: "top 90%",
          end: "top 45%",
          scrub: 1,
          markers: false
        }
      }
    )
  }, section.value)

  ScrollTrigger.refresh()
})

onBeforeUnmount(() => {
  animationContext?.revert()
})
</script>

<template>
  <section
    id="recursos"
    class="features-section"
  >
    <div class="features-heading">
      <span class="section-label">
        Criado para jogar
      </span>

      <h2>
        Tecnologia que acompanha seus movimentos.
      </h2>

      <p>
        Cada detalhe foi pensado para transformar comandos rápidos
        em uma experiência confortável e envolvente.
      </p>
    </div>

    <div class="features-grid">
      <article
        v-for="feature in features"
        :key="feature.number"
        class="feature-card"
      >
        <span class="feature-number">
          {{ feature.number }}
        </span>

        <div class="feature-content">
          <h3>
            {{ feature.title }}
          </h3>

          <p>
            {{ feature.description }}
          </p>
        </div>
      </article>
    </div>
  </section>
</template>

<!-- STYELEES -->
<style scoped>
.features-section {
  position: relative;
  min-height: 100vh;
  overflow: hidden;
  padding: 120px 8%;

  background:
    radial-gradient(
      circle at 50% 0%,
      rgb(91 64 255 / 20%),
      transparent 38%
    ),
    #08080d;
}

.features-heading {
  max-width: 900px;
}

.section-label {
  color: #9588ff;
  font-size: 0.77rem;
  font-weight: 800;
  letter-spacing: 0.18rem;
  text-transform: uppercase;
}

.features-heading h2 {
  max-width: 900px;
  margin-top: 20px;

  font-size: clamp(3rem, 6vw, 6rem);
  line-height: 0.92;
  letter-spacing: -0.06em;
}

.features-heading > p {
  max-width: 620px;
  margin-top: 26px;

  color: #9c9ca8;
  font-size: 1rem;
  line-height: 1.8;
}

.features-grid {
  display: grid;
  margin-top: 80px;
  grid-template-columns: repeat(3, 1fr);
  gap: 20px;

  perspective: 1000px;
}

.feature-card {
  position: relative;

  display: flex;
  min-height: 330px;
  justify-content: space-between;
  flex-direction: column;

  overflow: hidden;
  padding: 34px;

  border: 1px solid rgb(255 255 255 / 9%);
  border-radius: 28px;

  background:
    linear-gradient(
      145deg,
      rgb(255 255 255 / 8%),
      rgb(255 255 255 / 2%)
    );

  transition:
    transform 0.3s ease,
    border-color 0.3s ease,
    background-color 0.3s ease;
}

.feature-card::before {
  position: absolute;
  top: -80px;
  right: -80px;

  width: 180px;
  height: 180px;

  border-radius: 50%;
  background-color: rgb(119 102 255 / 12%);

  content: "";
  filter: blur(20px);
  transition: transform 0.4s ease;
}

.feature-card:hover {
  border-color: rgb(119 102 255 / 60%);
  transform: translateY(-10px);
}

.feature-card:hover::before {
  transform: scale(1.5);
}

.feature-number {
  position: relative;
  z-index: 1;

  color: #7766ff;
  font-size: 0.78rem;
  font-weight: 800;
  letter-spacing: 0.08rem;
}

.feature-content {
  position: relative;
  z-index: 1;
}

.feature-content h3 {
  font-size: 1.5rem;
}

.feature-content p {
  margin-top: 16px;

  color: #9999a5;
  font-size: 0.92rem;
  line-height: 1.7;
}

@media (max-width: 900px) {
  .features-grid {
    grid-template-columns: 1fr;
  }

  .feature-card {
    min-height: 260px;
  }
}

@media (max-width: 600px) {
  .features-section {
    padding: 100px 24px;
  }

  .features-grid {
    margin-top: 55px;
  }
}
</style>