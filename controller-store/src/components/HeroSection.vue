<script setup>
import { onBeforeUnmount, onMounted, ref } from "vue"
import gsap from "gsap"

const heroSection = ref(null)
let animationContext

function goToProduct() {
  const productSection = document.querySelector("#produto")

  productSection?.scrollIntoView({
    behavior: "smooth"
  })
}

onMounted(() => {
  animationContext = gsap.context(() => {
    const entranceTimeline = gsap.timeline({
      defaults: {
        duration: 1,
        ease: "power3.out"
      }
    })

    entranceTimeline
      .from(".hero-label", {
        y: 30,
        opacity: 0
      })

      .from(
        ".hero-title > span",
        {
          y: 110,
          opacity: 0,
          stagger: 0.15
        },
        "-=0.65"
      )

      .from(
        ".hero-description",
        {
          y: 45,
          opacity: 0
        },
        "-=0.65"
      )

      .from(
        ".hero-actions > *",
        {
          y: 30,
          opacity: 0,
          stagger: 0.15
        },
        "-=0.7"
      )

      .from(
        ".controller-placeholder",
        {
          x: 100,
          scale: 0.72,
          rotation: 8,
          opacity: 0,
          duration: 1.3
        },
        "-=1.1"
      )

      .from(
        ".preview-message",
        {
          y: 20,
          opacity: 0
        },
        "-=0.4"
      )

    gsap.to(".controller-body", {
        y: -14,
        rotationZ: -6,
        duration: 2.4,
        repeat: -1,
        yoyo: true,
        ease: "sine.inOut"
    })

    gsap.to(".hero-light-one", {
      scale: 1.2,
      opacity: 0.55,
      duration: 3.2,
      repeat: -1,
      yoyo: true,
      ease: "sine.inOut"
    })
  }, heroSection.value)
})

onBeforeUnmount(() => {
  animationContext?.revert()
})
</script>

<template>
  <section
    id="inicio"
    class="hero"
    ref="heroSection"
  >
    <div class="hero-light hero-light-one"></div>
    <div class="hero-light hero-light-two"></div>

    <div class="hero-content">
      <span class="hero-label">
        Controle sem limites
      </span>

      <h1 class="hero-title">
        <span>Domine</span>
        <span class="outlined-text">
          cada partida.
        </span>
      </h1>

      <p class="hero-description">
        Um controle conceitual desenvolvido para oferecer
        precisão, conforto e liberdade durante suas partidas.
      </p>

      <div class="hero-actions">
        <a
          href="#comprar"
          class="primary-button"
        >
          Comprar agora
        </a>

        <button
          type="button"
          class="secondary-button"
          @click="goToProduct"
        >
          Explorar produto
        </button>
      </div>
    </div>

    <div class="product-preview">
      <div class="controller-placeholder">
        <div class="controller-body">
          <div class="analog analog-left"></div>
          <div class="analog analog-right"></div>

          <div class="button-group">
            <span class="game-button button-top"></span>
            <span class="game-button button-right"></span>
            <span class="game-button button-bottom"></span>
            <span class="game-button button-left"></span>
          </div>

          <div class="touchpad"></div>
        </div>
      </div>

      <p class="preview-message">
        Em breve, o modelo 3D aparecerá aqui.
      </p>
    </div>
  </section>
</template>

<style scoped>
.hero {
  position: relative;

  display: grid;
  min-height: 100vh;
  grid-template-columns: 0.9fr 1.1fr;
  align-items: center;
  gap: 40px;

  overflow: hidden;
  padding: 120px 6% 60px;
}

.hero-light {
  position: absolute;
  border-radius: 50%;
  pointer-events: none;
  filter: blur(140px);
}

.hero-light-one {
  top: 5%;
  right: 5%;
  width: 500px;
  height: 500px;
  background-color: rgb(83 59 255 / 30%);
}

.hero-light-two {
  bottom: 5%;
  left: 20%;
  width: 300px;
  height: 300px;
  background-color: rgb(0 153 255 / 15%);
}

.hero-content {
  position: relative;
  z-index: 2;
}

.hero-label {
  color: #9588ff;
  font-size: 0.77rem;
  font-weight: 800;
  letter-spacing: 0.18rem;
  text-transform: uppercase;
}

.hero-title {
  display: flex;
  margin-top: 20px;
  flex-direction: column;

  font-size: clamp(4rem, 8vw, 8rem);
  line-height: 0.86;
  letter-spacing: -0.075em;
}

.outlined-text {
  color: transparent;
  -webkit-text-stroke: 1px rgb(255 255 255 / 70%);
}

.hero-description {
  max-width: 560px;
  margin-top: 32px;

  color: #aaaab5;
  font-size: 1rem;
  line-height: 1.8;
}

.hero-actions {
  display: flex;
  margin-top: 36px;
  flex-wrap: wrap;
  gap: 14px;
}

.primary-button,
.secondary-button {
  padding: 15px 23px;
  ;

  transition:
    transform 0.25s ease,
    border-color 0.25s ease;
}

.primary-button {
  background: linear-gradient(
    135deg,
    #806fff,
    #4c34ff
  );
  color: #ffffff;
}

.secondary-button {
  border: 1px solid rgb(255 255 255 / 15%);
  background-color: rgb(255 255 255 / 5%);
  color: #ffffff;
}

.primary-button:hover,
.secondary-button:hover {
  transform: translateY(-3px);
}

.secondary-button:hover {
  border-color: rgb(255 255 255 / 40%);
}

.product-preview {
  position: relative;
  z-index: 2;

  display: flex;
  min-height: 560px;
  align-items: center;
  justify-content: center;
  flex-direction: column;
}

.controller-placeholder {
  display: flex;
  width: min(100%, 620px);
  aspect-ratio: 1.2;
  align-items: center;
  justify-content: center;

  border: 1px solid rgb(255 255 255 / 8%);
  border-radius: 40px;

  background:
    radial-gradient(
      circle at center,
      rgb(109 89 255 / 22%),
      transparent 58%
    ),
    rgb(255 255 255 / 3%);

  perspective: 900px;
}

.controller-body {
  position: relative;

  width: 350px;
  height: 200px;

  border: 1px solid rgb(255 255 255 / 20%);
  border-radius: 80px 80px 110px 110px;

  background: linear-gradient(
    145deg,
    #7566ff,
    #2c2186
  );

  box-shadow:
    0 50px 80px rgb(0 0 0 / 45%),
    inset 0 3px 5px rgb(255 255 255 / 22%);

  transform: rotateX(12deg);
}

.analog {
  position: absolute;
  bottom: 42px;

  width: 52px;
  height: 52px;

  border: 7px solid #13131a;
  border-radius: 50%;
  background-color: #292932;

  box-shadow: inset 0 3px 6px rgb(255 255 255 / 12%);
}

.analog-left {
  left: 84px;
}

.analog-right {
  right: 84px;
}

.touchpad {
  position: absolute;
  top: 35px;
  left: 50%;

  width: 95px;
  height: 48px;

  border-radius: 12px;
  background-color: #1c1c24;

  transform: translateX(-50%);
}

.button-group {
  position: absolute;
  top: 44px;
  right: 48px;

  width: 77px;
  height: 77px;
}

.game-button {
  position: absolute;

  width: 20px;
  height: 20px;

  border-radius: 50%;
  box-shadow: 0 4px 8px rgb(0 0 0 / 35%);
}

.button-top {
  top: 0;
  left: 29px;
  background-color: #d57cff;
}

.button-right {
  top: 29px;
  right: 0;
  background-color: #ff5e7d;
}

.button-bottom {
  bottom: 0;
  left: 29px;
  background-color: #60e6ac;
}

.button-left {
  top: 29px;
  left: 0;
  background-color: #55caff;
}

.preview-message {
  margin-top: 24px;
  color: #777783;
  font-size: 0.78rem;
}

@media (max-width: 950px) {
  .hero {
    grid-template-columns: 1fr;
    padding-top: 150px;
  }

  .hero-title {
    font-size: clamp(3.8rem, 16vw, 7rem);
  }

  .product-preview {
    min-height: 460px;
  }
}

@media (max-width: 500px) {
  .controller-body {
    width: 280px;
    height: 165px;
  }

  .analog {
    bottom: 32px;
    width: 44px;
    height: 44px;
  }

  .analog-left {
    left: 65px;
  }

  .analog-right {
    right: 65px;
  }

  .touchpad {
    top: 28px;
    width: 75px;
    height: 40px;
  }

  .button-group {
    top: 34px;
    right: 35px;
    transform: scale(0.8);
  }
}

@media (prefers-reduced-motion: reduce) {
  .controller-body,
  .hero-light {
    animation: none;
  }
}
</style>