<script setup>
import { ref } from "vue"

import HeaderNav from "./components/HeaderNav.vue"
import HeroSection from "./components/HeroSection.vue"
import ProductSection from "./components/ProductSection.vue"
import FeaturesSection from "./components/FeaturesSection.vue"
import PurchaseSection from "./components/PurchaseSection.vue"
import { computed } from "vue"
import CartDrawer from "./components/CartDrawer.vue"
import TechnicalSection from "./components/TechnicalSection.vue"
import FooterSection from "./components/FooterSection.vue"

import { productColors } from "./data/productColors"

const selectedColor = ref(productColors[0])

const cartItems = ref([])
const isCartOpen = ref(false)
const checkoutMessage = ref("")

const cartCount = computed(() => {
  return cartItems.value.reduce((total, item) => {
    return total + item.quantity
  }, 0)
})

function updateSelectedColor(color) {
  selectedColor.value = color
}

function handleAddToCart(item) {
  const itemId = item.color.name
    .toLowerCase()
    .replaceAll(" ", "-")

  const existingItem = cartItems.value.find(
    (cartItem) => cartItem.id === itemId
  )

  if (existingItem) {
    existingItem.quantity = Math.min(
      existingItem.quantity + item.quantity,
      5
    )
  } else {
    cartItems.value.push({
      ...item,
      id: itemId
    })
  }

  isCartOpen.value = true
}

function openCart() {
  isCartOpen.value = true
}

function closeCart() {
  isCartOpen.value = false
}

function increaseCartItem(itemId) {
  const item = cartItems.value.find(
    (cartItem) => cartItem.id === itemId
  )

  if (item && item.quantity < 5) {
    item.quantity += 1
  }
}

function decreaseCartItem(itemId) {
  const item = cartItems.value.find(
    (cartItem) => cartItem.id === itemId
  )

  if (item && item.quantity > 1) {
    item.quantity -= 1
  }
}

function removeCartItem(itemId) {
  cartItems.value = cartItems.value.filter(
    (item) => item.id !== itemId
  )
}

function simulateCheckout() {
  checkoutMessage.value =
    "Demonstração concluída. Nenhuma cobrança foi realizada."

  window.setTimeout(() => {
    checkoutMessage.value = ""
  }, 4000)
}
</script>

<template>
  <HeaderNav :cart-count="cartCount" @open-cart="openCart" />

  <main>
    <HeroSection />
    <ProductSection :selectedColor="selectedColor" :colors="productColors" @select-color="updateSelectedColor" />
    <FeaturesSection />

    <TechnicalSection
      :selectedColor="selectedColor"
    />

    <PurchaseSection :selected-color="selectedColor" :colors="productColors" @select-color="updateSelectedColor"
      @add-to-cart="handleAddToCart" />

    <FooterSection />
  </main>
  <CartDrawer :is-open="isCartOpen" :items="cartItems" @close="closeCart" @increase="increaseCartItem"
    @decrease="decreaseCartItem" @remove="removeCartItem" @checkout="simulateCheckout" />

  <p v-if="checkoutMessage" class="global-message" aria-live="polite">
    {{ checkoutMessage }}
  </p>
</template>

<style scoped>
.temporary-section {
  display: flex;
  min-height: 70vh;
  align-items: center;
  justify-content: center;
  flex-direction: column;
  gap: 18px;

  padding: 100px 24px;
}

.temporary-section h2 {
  font-size: clamp(3rem, 8vw, 6rem);
  letter-spacing: -0.06em;
}

.temporary-section p {
  color: #8d8d98;
  text-align: center;
}

.purchase-section {
  background-color: #09090e;
}

.global-message {
  position: fixed;
  z-index: 1200;
  right: 24px;
  bottom: 24px;

  max-width: 380px;
  padding: 16px 20px;

  border: 1px solid rgb(114 227 173 / 30%);
  border-radius: 16px;

  background: #101b17;
  color: #72e3ad;

  box-shadow: 0 18px 40px rgb(0 0 0 / 35%);

  font-size: 0.82rem;
  line-height: 1.5;
}

@media (max-width: 600px) {
  .global-message {
    right: 16px;
    bottom: 16px;
    left: 16px;
  }
}
</style>