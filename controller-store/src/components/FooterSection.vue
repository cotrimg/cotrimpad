<script setup>
import {
  onBeforeUnmount,
  onMounted,
  ref
} from "vue"

import gsap from "gsap"
import { ScrollTrigger } from "gsap/ScrollTrigger"

gsap.registerPlugin(ScrollTrigger)

const footerSection = ref(null)
const currentYear = new Date().getFullYear()

let animationContext

function scrollToTop() {
  window.scrollTo({
    top: 0,
    behavior: "smooth"
  })
}

onMounted(() => {
  animationContext = gsap.context(() => {
    gsap.from(".footer-main > *", {
      y: 55,
      opacity: 0,
      duration: 0.9,
      stagger: 0.12,
      ease: "power3.out",

      scrollTrigger: {
        trigger: ".footer-main",
        start: "top 88%",
        once: true
      }
    })

    gsap.from(".footer-bottom", {
      y: 30,
      opacity: 0,
      duration: 0.8,
      ease: "power3.out",

      scrollTrigger: {
        trigger: ".footer-bottom",
        start: "top 95%",
        once: true
      }
    })
  }, footerSection.value)
})

onBeforeUnmount(() => {
  animationContext?.revert()
})
</script>

<template>
  <footer
    ref="footerSection"
    class="footer"
  >
    <div class="footer-light"></div>

    <div class="footer-main">
      <div class="footer-brand">
        <a
          href="#inicio"
          class="footer-logo"
          aria-label="COTRIMPAD, voltar ao início"
        >
          COTRIM<span>PAD</span>
        </a>

        <p>
          Uma experiência conceitual de produto criada
          para explorar a integração entre interfaces
          reativas, animações e renderização 3D.
        </p>

        <div class="technology-tags">
          <span>Vue.js</span>
          <span>Three.js</span>
          <span>GSAP</span>
          <span>Vite</span>
        </div>
      </div>

      <div class="footer-column">
        <span class="column-title">
          Navegação
        </span>

        <nav aria-label="Navegação do rodapé">
          <a href="#inicio">Início</a>
          <a href="#produto">Produto 3D</a>
          <a href="#recursos">Recursos</a>
          <a href="#comprar">Comprar</a>
        </nav>
      </div>

      <div class="footer-column">
        <span class="column-title">
          Projeto
        </span>

        <ul>
          <li>Modelagem procedural</li>
          <li>Animações no scroll</li>
          <li>Personalização de cor</li>
          <li>Carrinho demonstrativo</li>
        </ul>
      </div>

      <div class="footer-column">
        <span class="column-title">
          Conecte-se
        </span>

        <div class="social-links">
          <a
            href="https://github.com/cotrimg"
            target="_blank"
            rel="noopener noreferrer"
            aria-label="Abrir perfil no GitHub em uma nova guia"
          >
            <span>GitHub</span>
            <strong aria-hidden="true">↗</strong>
          </a>

          <a
            href="https://www.linkedin.com/in/guilhermecotrim1/"
            target="_blank"
            rel="noopener noreferrer"
            aria-label="Abrir perfil no LinkedIn em uma nova guia"
          >
            <span>LinkedIn</span>
            <strong aria-hidden="true">↗</strong>
          </a>
        </div>
      </div>
    </div>

    <div class="footer-bottom">
      <div class="copyright">
        <span>
          © {{ currentYear }} Guilherme Souza Cotrim
        </span>

        <span class="separator" aria-hidden="true"></span>

        <span>
          Projeto para portfólio
        </span>
      </div>

      <p class="concept-warning">
        Projeto conceitual. Não representa uma loja ou
        produto oficial da Sony ou PlayStation.
      </p>

      <button
        type="button"
        class="back-to-top"
        aria-label="Voltar ao início da página"
        @click="scrollToTop"
      >
        <span>Voltar ao topo</span>

        <strong aria-hidden="true">↑</strong>
      </button>
    </div>
  </footer>
</template>

<style scoped>
.footer {
  position: relative;

  overflow: hidden;
  padding: 100px 7% 34px;

  border-top: 1px solid rgb(255 255 255 / 8%);

  background:
    linear-gradient(
      180deg,
      #08080d,
      #050509
    );
}

.footer-light {
  position: absolute;
  top: -230px;
  left: 8%;

  width: 520px;
  height: 420px;

  border-radius: 50%;

  background: rgb(92 66 255 / 14%);

  filter: blur(130px);
  pointer-events: none;
}

.footer-main {
  position: relative;
  z-index: 2;

  display: grid;
  grid-template-columns:
    minmax(300px, 1.6fr)
    repeat(3, minmax(130px, 0.6fr));

  gap: 65px;

  padding-bottom: 75px;
}

.footer-brand {
  max-width: 450px;
}

.footer-logo {
  display: inline-block;

  color: #ffffff;

  font-size: 1.8rem;
  font-weight: 900;
  letter-spacing: -0.07em;
}

.footer-logo span {
  color: #7766ff;
}

.footer-brand > p {
  max-width: 440px;
  margin-top: 24px;

  color: #898994;

  font-size: 0.88rem;
  line-height: 1.8;
}

.technology-tags {
  display: flex;
  margin-top: 28px;
  flex-wrap: wrap;
  gap: 9px;
}

.technology-tags span {
  padding: 9px 13px;

  border: 1px solid rgb(255 255 255 / 9%);
  border-radius: 999px;

  background: rgb(255 255 255 / 3%);

  color: #a7a7b2;

  font-size: 0.68rem;
  font-weight: 700;

  transition:
    border-color 0.25s ease,
    color 0.25s ease,
    transform 0.25s ease;
}

.technology-tags span:hover {
  border-color: rgb(119 102 255 / 55%);
  color: #ffffff;
  transform: translateY(-2px);
}

.footer-column {
  display: flex;
  flex-direction: column;
}

.column-title {
  color: #777783;

  font-size: 0.66rem;
  font-weight: 800;
  letter-spacing: 0.14rem;
  text-transform: uppercase;
}

.footer-column nav,
.footer-column ul,
.social-links {
  display: flex;
  margin-top: 25px;
  flex-direction: column;
  gap: 15px;
}

.footer-column ul {
  padding: 0;
  list-style: none;
}

.footer-column nav a,
.footer-column li {
  color: #a1a1ac;
  font-size: 0.8rem;
}

.footer-column nav a {
  width: fit-content;

  transition:
    color 0.25s ease,
    transform 0.25s ease;
}

.footer-column nav a:hover {
  color: #ffffff;
  transform: translateX(5px);
}

.social-links a {
  display: flex;
  width: 100%;
  align-items: center;
  justify-content: space-between;
  gap: 15px;

  padding-bottom: 13px;

  border-bottom: 1px solid rgb(255 255 255 / 7%);

  color: #a1a1ac;
  font-size: 0.8rem;

  transition:
    color 0.25s ease,
    border-color 0.25s ease;
}

.social-links a:hover {
  border-color: rgb(119 102 255 / 50%);
  color: #ffffff;
}

.social-links strong {
  color: #7766ff;
  font-size: 1rem;

  transition: transform 0.25s ease;
}

.social-links a:hover strong {
  transform: translate(3px, -3px);
}

.footer-bottom {
  position: relative;
  z-index: 2;

  display: grid;
  grid-template-columns: auto 1fr auto;
  align-items: center;
  gap: 35px;

  padding-top: 28px;

  border-top: 1px solid rgb(255 255 255 / 8%);
}

.copyright {
  display: flex;
  align-items: center;
  gap: 12px;

  color: #70707b;
  font-size: 0.68rem;
}

.separator {
  width: 4px;
  height: 4px;

  border-radius: 50%;
  background: #7766ff;
}

.concept-warning {
  max-width: 540px;
  justify-self: center;

  color: #62626d;

  font-size: 0.65rem;
  line-height: 1.5;
  text-align: center;
}

.back-to-top {
  display: flex;
  align-items: center;
  gap: 11px;

  padding: 11px 15px;

  border: 1px solid rgb(255 255 255 / 10%);
  border-radius: 999px;

  background: rgb(255 255 255 / 3%);
  color: #b2b2bc;

  font-size: 0.7rem;

  transition:
    border-color 0.25s ease,
    background-color 0.25s ease,
    color 0.25s ease,
    transform 0.25s ease;
}

.back-to-top:hover {
  border-color: rgb(119 102 255 / 50%);
  background-color: rgb(119 102 255 / 8%);
  color: #ffffff;
  transform: translateY(-3px);
}

.back-to-top strong {
  color: #8c7fff;
  font-size: 0.95rem;
}

.back-to-top:focus-visible,
.footer-column a:focus-visible,
.footer-logo:focus-visible {
  outline: 3px solid #9588ff;
  outline-offset: 5px;
}

@media (max-width: 1100px) {
  .footer-main {
    grid-template-columns:
      minmax(280px, 1.4fr)
      repeat(2, 1fr);
  }

  .footer-column:last-child {
    grid-column: 2 / 4;
  }
}

@media (max-width: 780px) {
  .footer {
    padding: 85px 24px 30px;
  }

  .footer-main {
    grid-template-columns: 1fr 1fr;
    gap: 50px 30px;
    padding-bottom: 55px;
  }

  .footer-brand {
    grid-column: 1 / 3;
  }

  .footer-column:last-child {
    grid-column: auto;
  }

  .footer-bottom {
    grid-template-columns: 1fr;
    justify-items: start;
    gap: 20px;
  }

  .concept-warning {
    justify-self: start;
    text-align: left;
  }
}

@media (max-width: 480px) {
  .footer-main {
    grid-template-columns: 1fr;
  }

  .footer-brand {
    grid-column: auto;
  }

  .footer-column:last-child {
    grid-column: auto;
  }

  .copyright {
    align-items: flex-start;
    flex-direction: column;
    gap: 7px;
  }

  .separator {
    display: none;
  }
}
</style>