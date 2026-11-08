<script setup>
import {
  computed,
  nextTick,
  onBeforeUnmount,
  ref,
  watch
} from "vue"

const props = defineProps({
  isOpen: {
    type: Boolean,
    default: false
  },

  items: {
    type: Array,
    default: () => []
  }
})

const emit = defineEmits([
  "close",
  "increase",
  "decrease",
  "remove",
  "checkout"
])

const closeButton = ref(null)

const totalItems = computed(() => {
  return props.items.reduce((total, item) => {
    return total + item.quantity
  }, 0)
})

const subtotal = computed(() => {
  return props.items.reduce((total, item) => {
    return total + item.unitPrice * item.quantity
  }, 0)
})

const formattedSubtotal = computed(() => {
  return new Intl.NumberFormat("pt-BR", {
    style: "currency",
    currency: "BRL"
  }).format(subtotal.value)
})

function formatPrice(price) {
  return new Intl.NumberFormat("pt-BR", {
    style: "currency",
    currency: "BRL"
  }).format(price)
}

function closeCart() {
  emit("close")
}

function handleKeydown(event) {
  if (event.key === "Escape" && props.isOpen) {
    closeCart()
  }
}

watch(
  () => props.isOpen,
  async (isOpen) => {
    document.body.style.overflow = isOpen
      ? "hidden"
      : ""

    if (isOpen) {
      await nextTick()
      closeButton.value?.focus()
    }
  }
)

window.addEventListener("keydown", handleKeydown)

onBeforeUnmount(() => {
  window.removeEventListener(
    "keydown",
    handleKeydown
  )

  document.body.style.overflow = ""
})
</script>

<template>
  <Teleport to="body">
    <Transition name="cart">
      <div
        v-if="isOpen"
        class="cart-layer"
      >
        <button
          type="button"
          class="cart-backdrop"
          aria-label="Fechar carrinho"
          @click="closeCart"
        ></button>

        <aside
          class="cart-drawer"
          role="dialog"
          aria-modal="true"
          aria-labelledby="cart-title"
        >
          <header class="cart-header">
            <div>
              <span>Seu carrinho</span>

              <h2 id="cart-title">
                {{ totalItems }}
                {{ totalItems === 1 ? "item" : "itens" }}
              </h2>
            </div>

            <button
              ref="closeButton"
              type="button"
              class="close-button"
              aria-label="Fechar carrinho"
              @click="closeCart"
            >
              ×
            </button>
          </header>

          <div
            v-if="items.length === 0"
            class="empty-cart"
          >
            <span class="empty-icon">0</span>

            <h3>Seu carrinho está vazio</h3>

            <p>
              Configure a cor do controle e adicione
              o produto para continuar.
            </p>

            <button
              type="button"
              @click="closeCart"
            >
              Continuar explorando
            </button>
          </div>

          <div
            v-else
            class="cart-content"
          >
            <ul class="cart-items">
              <li
                v-for="item in items"
                :key="item.id"
                class="cart-item"
              >
                <div
                  class="product-color"
                  :style="{
                    backgroundColor: item.color.value
                  }"
                >
                  <span></span>
                </div>

                <div class="item-information">
                  <div class="item-heading">
                    <div>
                      <h3>{{ item.name }}</h3>

                      <p>
                        Cor: {{ item.color.name }}
                      </p>
                    </div>

                    <button
                      type="button"
                      class="remove-button"
                      :aria-label="
                        `Remover controle ${item.color.name}`
                      "
                      @click="emit('remove', item.id)"
                    >
                      Remover
                    </button>
                  </div>

                  <div class="item-footer">
                    <div class="quantity-control">
                      <button
                        type="button"
                        aria-label="Diminuir quantidade"
                        :disabled="item.quantity === 1"
                        @click="emit('decrease', item.id)"
                      >
                        −
                      </button>

                      <strong>
                        {{ item.quantity }}
                      </strong>

                      <button
                        type="button"
                        aria-label="Aumentar quantidade"
                        :disabled="item.quantity === 5"
                        @click="emit('increase', item.id)"
                      >
                        +
                      </button>
                    </div>

                    <strong class="item-total">
                      {{
                        formatPrice(
                          item.unitPrice * item.quantity
                        )
                      }}
                    </strong>
                  </div>
                </div>
              </li>
            </ul>

            <div class="cart-summary">
              <div class="summary-line">
                <span>Subtotal</span>
                <strong>{{ formattedSubtotal }}</strong>
              </div>

              <p>
                Projeto demonstrativo. Nenhum pagamento
                será processado.
              </p>

              <button
                type="button"
                class="checkout-button"
                @click="emit('checkout')"
              >
                Finalizar demonstração
              </button>
            </div>
          </div>
        </aside>
      </div>
    </Transition>
  </Teleport>
</template>

<style scoped>
.cart-layer {
  position: fixed;
  z-index: 1000;
  inset: 0;
}

.cart-backdrop {
  position: absolute;
  inset: 0;

  width: 100%;
  height: 100%;

  background: rgb(0 0 0 / 68%);
  backdrop-filter: blur(7px);
}

.cart-drawer {
  position: absolute;
  top: 0;
  right: 0;

  display: flex;
  width: min(100%, 480px);
  height: 100%;
  flex-direction: column;

  border-left: 1px solid rgb(255 255 255 / 10%);
  background:
    radial-gradient(
      circle at 100% 0%,
      rgb(103 87 255 / 20%),
      transparent 32%
    ),
    #09090e;

  color: #ffffff;
  box-shadow: -30px 0 80px rgb(0 0 0 / 45%);
}

.cart-header {
  display: flex;
  align-items: center;
  justify-content: space-between;

  padding: 28px;

  border-bottom: 1px solid rgb(255 255 255 / 9%);
}

.cart-header span {
  color: #8b8b96;
  font-size: 0.72rem;
  letter-spacing: 0.12rem;
  text-transform: uppercase;
}

.cart-header h2 {
  margin-top: 5px;
  font-size: 1.25rem;
}

.close-button {
  display: flex;
  width: 42px;
  height: 42px;
  align-items: center;
  justify-content: center;

  border: 1px solid rgb(255 255 255 / 10%);
  border-radius: 50%;
  background: rgb(255 255 255 / 5%);
  color: #ffffff;

  font-size: 1.5rem;
  line-height: 1;
}

.close-button:hover {
  background: rgb(255 255 255 / 10%);
}

.close-button:focus-visible,
.checkout-button:focus-visible,
.empty-cart button:focus-visible,
.quantity-control button:focus-visible,
.remove-button:focus-visible {
  outline: 3px solid #9b8cff;
  outline-offset: 3px;
}

.empty-cart {
  display: flex;
  flex: 1;
  align-items: center;
  justify-content: center;
  flex-direction: column;

  padding: 40px;
  text-align: center;
}

.empty-icon {
  display: flex;
  width: 74px;
  height: 74px;
  align-items: center;
  justify-content: center;

  border: 1px solid rgb(119 102 255 / 35%);
  border-radius: 50%;

  color: #8d7fff;
  font-size: 1.5rem;
  font-weight: 800;
}

.empty-cart h3 {
  margin-top: 24px;
  font-size: 1.4rem;
}

.empty-cart p {
  max-width: 310px;
  margin-top: 12px;

  color: #90909b;
  font-size: 0.88rem;
  line-height: 1.7;
}

.empty-cart button {
  margin-top: 26px;
  padding: 14px 21px;

  border-radius: 999px;
  background: #ffffff;
  color: #09090e;

  font-weight: 800;
}

.cart-content {
  display: flex;
  min-height: 0;
  flex: 1;
  flex-direction: column;
}

.cart-items {
  display: flex;
  overflow-y: auto;
  flex: 1;
  flex-direction: column;
  gap: 15px;

  padding: 24px;

  list-style: none;
}

.cart-item {
  display: grid;
  grid-template-columns: 80px 1fr;
  gap: 17px;

  padding: 16px;

  border: 1px solid rgb(255 255 255 / 9%);
  border-radius: 22px;
  background: rgb(255 255 255 / 3%);
}

.product-color {
  position: relative;

  min-height: 98px;
  overflow: hidden;

  border-radius: 16px;
  box-shadow:
    inset 0 3px 8px rgb(255 255 255 / 25%),
    0 10px 22px rgb(0 0 0 / 25%);
}

.product-color::after {
  position: absolute;
  inset: 20%;

  border: 4px solid rgb(255 255 255 / 28%);
  border-radius: 50%;

  content: "";
}

.product-color span {
  position: absolute;
  right: 18%;
  bottom: 18%;

  width: 17px;
  height: 17px;

  border-radius: 50%;
  background: rgb(12 12 17 / 65%);
}

.item-information {
  min-width: 0;
}

.item-heading {
  display: flex;
  align-items: flex-start;
  justify-content: space-between;
  gap: 12px;
}

.item-heading h3 {
  font-size: 0.92rem;
}

.item-heading p {
  margin-top: 6px;
  color: #898995;
  font-size: 0.72rem;
}

.remove-button {
  background: transparent;
  color: #ff7b90;

  font-size: 0.68rem;
}

.item-footer {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 15px;

  margin-top: 24px;
}

.quantity-control {
  display: flex;
  align-items: center;
  gap: 12px;
}

.quantity-control button {
  display: flex;
  width: 28px;
  height: 28px;
  align-items: center;
  justify-content: center;

  border-radius: 50%;
  background: rgb(255 255 255 / 8%);
  color: #ffffff;
}

.quantity-control button:disabled {
  cursor: not-allowed;
  opacity: 0.35;
}

.quantity-control strong {
  min-width: 15px;
  text-align: center;
}

.item-total {
  font-size: 0.85rem;
}

.cart-summary {
  padding: 24px;

  border-top: 1px solid rgb(255 255 255 / 9%);
  background: rgb(5 5 9 / 65%);
}

.summary-line {
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.summary-line span {
  color: #9a9aa5;
}

.summary-line strong {
  font-size: 1.5rem;
}

.cart-summary p {
  margin-top: 12px;

  color: #70707b;
  font-size: 0.7rem;
  line-height: 1.5;
}

.checkout-button {
  width: 100%;
  margin-top: 20px;
  padding: 17px 22px;

  border-radius: 999px;
  background: linear-gradient(
    135deg,
    #806fff,
    #4b34ff
  );

  color: #ffffff;
  font-weight: 800;
}

.cart-enter-active,
.cart-leave-active {
  transition: opacity 0.35s ease;
}

.cart-enter-from,
.cart-leave-to {
  opacity: 0;
}

.cart-enter-active .cart-drawer,
.cart-leave-active .cart-drawer {
  transition: transform 0.4s ease;
}

.cart-enter-from .cart-drawer,
.cart-leave-to .cart-drawer {
  transform: translateX(100%);
}

@media (max-width: 500px) {
  .cart-header,
  .cart-summary {
    padding: 20px;
  }

  .cart-items {
    padding: 16px;
  }

  .cart-item {
    grid-template-columns: 65px 1fr;
  }
}
</style>