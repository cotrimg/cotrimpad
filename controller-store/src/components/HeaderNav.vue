<script setup>
import {
  onBeforeUnmount,
  ref,
  watch
} from "vue"

defineProps({
  cartCount: {
    type: Number,
    default: 0
  }
})

const emit = defineEmits(["open-cart"])

const isMenuOpen = ref(false)

function toggleMenu() {
  isMenuOpen.value = !isMenuOpen.value
}

function closeMenu() {
  isMenuOpen.value = false
}

function openCart() {
  closeMenu()
  emit("open-cart")
}

function handleKeydown(event) {
  if (
    event.key === "Escape" &&
    isMenuOpen.value
  ) {
    closeMenu()
  }
}

watch(isMenuOpen, (isOpen) => {
  document.body.style.overflow = isOpen
    ? "hidden"
    : ""
})

window.addEventListener(
  "keydown",
  handleKeydown
)

onBeforeUnmount(() => {
  document.body.style.overflow = ""
  window.removeEventListener(
    "keydown",
    handleKeydown
  )
  window.removeEventListener(
    "resize",
    handleResize
  )
})

function handleResize() {
  if (
    window.innerWidth > 800 &&
    isMenuOpen.value
  ) {
    closeMenu()
  }
}

window.addEventListener(
  "resize",
  handleResize
)
</script>

<template>
  <header class="header">
    <a
      href="#inicio"
      class="logo"
      aria-label="COTRIMPAD, voltar ao início"
      @click="closeMenu"
    >
      COTRIM<span>PAD</span>
    </a>

    <nav
      class="desktop-navigation"
      aria-label="Navegação principal"
    >
      <a href="#inicio">
        Início
      </a>

      <a href="#produto">
        Produto
      </a>

      <a href="#recursos">
        Recursos
      </a>

      <a href="#comprar">
        Comprar
      </a>
    </nav>

    <div class="header-actions">
      <button
        type="button"
        class="cart-button"
        aria-label="Abrir carrinho"
        @click="openCart"
      >
        <span class="desktop-cart-text">
          Carrinho
        </span>

        <span
          class="cart-icon"
          aria-hidden="true"
        >
          🛒
        </span>

        <span
          v-if="cartCount > 0"
          class="cart-count"
        >
          {{ cartCount > 9 ? "9+" : cartCount }}
        </span>
      </button>

      <button
        type="button"
        class="menu-button"
        :class="{ active: isMenuOpen }"
        :aria-expanded="isMenuOpen"
        aria-controls="mobile-navigation"
        :aria-label="
          isMenuOpen
            ? 'Fechar menu'
            : 'Abrir menu'
        "
        @click="toggleMenu"
      >
        <span></span>
        <span></span>
        <span></span>
      </button>
    </div>
  </header>

  <Transition name="mobile-menu">
    <div
      v-if="isMenuOpen"
      class="mobile-menu-layer"
    >
      <button
        type="button"
        class="mobile-backdrop"
        aria-label="Fechar menu"
        @click="closeMenu"
      ></button>

      <nav
        id="mobile-navigation"
        class="mobile-navigation"
        aria-label="Navegação para celular"
      >
        <div class="mobile-menu-heading">
          <span>Navegação</span>

          <strong>COTRIMPAD</strong>
        </div>

        <div class="mobile-links">
          <a
            href="#inicio"
            @click="closeMenu"
          >
            <span>01</span>
            Início
          </a>

          <a
            href="#produto"
            @click="closeMenu"
          >
            <span>02</span>
            Produto
          </a>

          <a
            href="#recursos"
            @click="closeMenu"
          >
            <span>03</span>
            Recursos
          </a>

          <a
            href="#comprar"
            @click="closeMenu"
          >
            <span>04</span>
            Comprar
          </a>
        </div>

        <button
          type="button"
          class="mobile-cart-button"
          @click="openCart"
        >
          <span>
            Abrir carrinho
          </span>

          <strong>
            {{ cartCount }}
            {{ cartCount === 1 ? "item" : "itens" }}
          </strong>
        </button>

        <p class="mobile-menu-footer">
          Projeto conceitual desenvolvido com
          Vue.js, Three.js e GSAP.
        </p>
      </nav>
    </div>
  </Transition>
</template>

<style scoped>
.header {
  position: fixed;
  z-index: 500;
  top: 0;
  left: 0;

  display: flex;
  width: 100%;
  min-height: 78px;
  align-items: center;
  justify-content: space-between;

  padding: 0 6%;

  border-bottom:
    1px solid rgb(255 255 255 / 8%);

  background: rgb(5 5 9 / 72%);
  backdrop-filter: blur(18px);
}

.logo {
  position: relative;
  z-index: 502;

  color: #ffffff;

  font-size: 1.15rem;
  font-weight: 900;
  letter-spacing: -0.05em;
}

.logo span {
  color: #7766ff;
}

.desktop-navigation {
  display: flex;
  align-items: center;
  gap: 32px;
}

.desktop-navigation a {
  position: relative;

  padding: 10px 0;

  color: #aaaab5;
  font-size: 0.85rem;

  transition: color 0.25s ease;
}

.desktop-navigation a::after {
  position: absolute;
  right: 0;
  bottom: 4px;
  left: 0;

  height: 2px;

  border-radius: 999px;
  background: #7766ff;

  content: "";

  transform: scaleX(0);
  transition: transform 0.25s ease;
}

.desktop-navigation a:hover {
  color: #ffffff;
}

.desktop-navigation a:hover::after {
  transform: scaleX(1);
}

.header-actions {
  position: relative;
  z-index: 502;

  display: flex;
  align-items: center;
  gap: 12px;
}

.cart-button {
  position: relative;

  display: flex;
  min-height: 42px;
  align-items: center;
  justify-content: center;
  gap: 8px;

  padding: 0 19px;

  border-radius: 999px;
  background: #ffffff;
  color: #09090d;

  font-size: 0.8rem;
  font-weight: 800;

  transition:
    background-color 0.25s ease,
    transform 0.25s ease;
}

.cart-button:hover {
  background: #dedee6;
  transform: translateY(-2px);
}

.cart-icon {
  display: none;
  font-size: 1rem;
}

.cart-count {
  position: absolute;
  top: -8px;
  right: -8px;

  display: flex;
  min-width: 23px;
  height: 23px;
  align-items: center;
  justify-content: center;

  padding: 0 6px;

  border: 3px solid #050509;
  border-radius: 999px;

  background: #7766ff;
  color: #ffffff;

  font-size: 0.62rem;
  font-weight: 900;
}

.menu-button {
  position: relative;

  display: none;
  width: 46px;
  height: 46px;

  border:
    1px solid rgb(255 255 255 / 12%);
  border-radius: 50%;

  background: rgb(255 255 255 / 5%);
}

.menu-button span {
  position: absolute;
  left: 50%;

  width: 19px;
  height: 2px;

  border-radius: 999px;
  background: #ffffff;

  transform: translateX(-50%);
  transition:
    top 0.3s ease,
    transform 0.3s ease,
    opacity 0.25s ease;
}

.menu-button span:nth-child(1) {
  top: 15px;
}

.menu-button span:nth-child(2) {
  top: 22px;
}

.menu-button span:nth-child(3) {
  top: 29px;
}

.menu-button.active span:nth-child(1) {
  top: 22px;
  transform:
    translateX(-50%)
    rotate(45deg);
}

.menu-button.active span:nth-child(2) {
  opacity: 0;
}

.menu-button.active span:nth-child(3) {
  top: 22px;
  transform:
    translateX(-50%)
    rotate(-45deg);
}

.mobile-menu-layer {
  position: fixed;
  z-index: 490;
  inset: 0;
}

.mobile-backdrop {
  position: absolute;
  inset: 0;

  width: 100%;
  height: 100%;

  background: rgb(0 0 0 / 70%);
  backdrop-filter: blur(7px);
}

.mobile-navigation {
  position: absolute;
  top: 0;
  right: 0;

  display: flex;
  width: min(88%, 420px);
  height: 100%;
  flex-direction: column;

  padding: 110px 30px 30px;

  border-left:
    1px solid rgb(255 255 255 / 9%);

  background:
    radial-gradient(
      circle at 100% 0%,
      rgb(103 87 255 / 22%),
      transparent 34%
    ),
    #09090e;

  box-shadow:
    -30px 0 80px rgb(0 0 0 / 45%);
}

.mobile-menu-heading {
  display: flex;
  align-items: flex-end;
  justify-content: space-between;
  gap: 20px;

  padding-bottom: 22px;

  border-bottom:
    1px solid rgb(255 255 255 / 9%);
}

.mobile-menu-heading span {
  color: #777783;

  font-size: 0.65rem;
  letter-spacing: 0.14rem;
  text-transform: uppercase;
}

.mobile-menu-heading strong {
  font-size: 0.82rem;
}

.mobile-links {
  display: flex;
  margin-top: 24px;
  flex-direction: column;
}

.mobile-links a {
  display: flex;
  align-items: center;
  gap: 20px;

  padding: 20px 0;

  border-bottom:
    1px solid rgb(255 255 255 / 7%);

  color: #ffffff;
  font-size: 1.7rem;
  font-weight: 700;
  letter-spacing: -0.04em;

  transition:
    color 0.25s ease,
    padding-left 0.25s ease;
}

.mobile-links a:hover {
  padding-left: 8px;
  color: #9b8cff;
}

.mobile-links span {
  color: #7766ff;

  font-size: 0.62rem;
  font-weight: 800;
  letter-spacing: 0;
}

.mobile-cart-button {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 20px;

  margin-top: auto;
  padding: 17px 20px;

  border-radius: 999px;

  background:
    linear-gradient(
      135deg,
      #806fff,
      #4b34ff
    );

  color: #ffffff;
}

.mobile-cart-button span {
  font-weight: 800;
}

.mobile-cart-button strong {
  font-size: 0.72rem;
}

.mobile-menu-footer {
  margin-top: 20px;

  color: #6f6f7a;
  font-size: 0.68rem;
  line-height: 1.6;
}

/* Fundo do menu */
.mobile-menu-enter-active,
.mobile-menu-leave-active {
  transition: opacity 0.35s ease;
}

.mobile-menu-enter-from,
.mobile-menu-leave-to {
  opacity: 0;
}

/* Painel lateral */
.mobile-menu-enter-active
.mobile-navigation,
.mobile-menu-leave-active
.mobile-navigation {
  transition:
    transform 0.42s ease;
}

.mobile-menu-enter-from
.mobile-navigation,
.mobile-menu-leave-to
.mobile-navigation {
  transform: translateX(100%);
}

@media (max-width: 800px) {
  .header {
    min-height: 70px;
    padding: 0 20px;
  }

  .desktop-navigation {
    display: none;
  }

  .menu-button {
    display: block;
  }

  .desktop-cart-text {
    display: none;
  }

  .cart-icon {
    display: inline;
  }

  .cart-button {
    width: 46px;
    min-height: 46px;
    padding: 0;
  }
}

@media (min-width: 801px) {
  .mobile-menu-layer {
    display: none;
  }
}

@media (max-width: 400px) {
  .mobile-navigation {
    width: 100%;
  }

  .mobile-links a {
    font-size: 1.45rem;
  }
}
</style>