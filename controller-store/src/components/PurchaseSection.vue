<script setup>
import { computed, ref } from "vue"
import ControllerViewer from "./ControllerViewer.vue"

const props = defineProps({
  selectedColor: {
    type: Object,
    required: true
  },

  colors: {
    type: Array,
    required: true
  }
})

const emit = defineEmits([
  "select-color",
  "add-to-cart"
])

const quantity = ref(1)
const addedMessage = ref("")

const unitPrice = 299.9

const formattedUnitPrice = computed(() => {
  return new Intl.NumberFormat("pt-BR", {
    style: "currency",
    currency: "BRL"
  }).format(unitPrice)
})

const formattedTotal = computed(() => {
  return new Intl.NumberFormat("pt-BR", {
    style: "currency",
    currency: "BRL"
  }).format(unitPrice * quantity.value)
})

function decreaseQuantity() {
  if (quantity.value > 1) {
    quantity.value -= 1
  }
}

function increaseQuantity() {
  if (quantity.value < 5) {
    quantity.value += 1
  }
}

function selectColor(color) {
  emit("select-color", color)
}

function addToCart() {
  const item = {
    name: "COTRIMPAD Wireless",
    color: props.selectedColor,
    quantity: quantity.value,
    unitPrice
  }

  emit("add-to-cart", item)

  addedMessage.value =
    `${quantity.value} controle(s) na cor ` +
    `${props.selectedColor.name} adicionado(s).`

  window.setTimeout(() => {
    addedMessage.value = ""
  }, 3500)
}
</script>

<template>
  <section
    id="comprar"
    class="purchase-section"
  >
    <div class="purchase-preview">
      <ControllerViewer
        :color="selectedColor.value"
      />
    </div>

    <div class="purchase-content">
      <span class="purchase-label">
        Configure seu controle
      </span>

      <h2>COTRIMPAD Wireless</h2>

      <p class="purchase-description">
        Controle conceitual sem fio com acabamento
        personalizável e experiência tridimensional.
      </p>

      <div class="configuration-block">
        <div class="configuration-heading">
          <span>Cor</span>
          <strong>{{ selectedColor.name }}</strong>
        </div>

        <div class="purchase-colors">
          <button
            v-for="color in colors"
            :key="color.name"
            type="button"
            class="purchase-color"
            :class="{
              active:
                selectedColor.name === color.name
            }"
            :style="{
              backgroundColor: color.value
            }"
            :aria-label="
              `Selecionar a cor ${color.name}`
            "
            :aria-pressed="
              selectedColor.name === color.name
            "
            @click="selectColor(color)"
          ></button>
        </div>
      </div>

      <div class="quantity-area">
        <span>Quantidade</span>

        <div class="quantity-control">
          <button
            type="button"
            aria-label="Diminuir quantidade"
            :disabled="quantity === 1"
            @click="decreaseQuantity"
          >
            −
          </button>

          <strong aria-live="polite">
            {{ quantity }}
          </strong>

          <button
            type="button"
            aria-label="Aumentar quantidade"
            :disabled="quantity === 5"
            @click="increaseQuantity"
          >
            +
          </button>
        </div>
      </div>

      <div class="price-area">
        <div>
          <span>Preço unitário</span>
          <strong>{{ formattedUnitPrice }}</strong>
        </div>

        <div>
          <span>Total</span>
          <strong>{{ formattedTotal }}</strong>
        </div>
      </div>

      <p class="installment-text">
        Valor demonstrativo. Pagamento não será processado.
      </p>

      <button
        type="button"
        class="add-button"
        @click="addToCart"
      >
        Adicionar ao carrinho
      </button>

      <p
        class="added-message"
        aria-live="polite"
      >
        {{ addedMessage }}
      </p>
    </div>
  </section>
</template>

<style scoped>
.purchase-section {
  display: grid;
  min-height: 100vh;
  grid-template-columns: 1.05fr 0.95fr;
  align-items: center;
  gap: 70px;

  padding: 120px 8%;

  background:
    radial-gradient(
      circle at 15% 50%,
      rgb(91 64 255 / 20%),
      transparent 35%
    ),
    #050509;
}

.purchase-preview {
  min-width: 0;
}

.purchase-label {
  color: #9588ff;
  font-size: 0.77rem;
  font-weight: 800;
  letter-spacing: 0.18rem;
  text-transform: uppercase;
}

.purchase-content h2 {
  max-width: 620px;
  margin-top: 18px;

  font-size: clamp(3rem, 5vw, 5.5rem);
  line-height: 0.92;
  letter-spacing: -0.06em;
}

.purchase-description {
  max-width: 580px;
  margin-top: 25px;

  color: #9999a5;
  line-height: 1.75;
}

.configuration-block {
  max-width: 500px;
  margin-top: 38px;
  padding-top: 28px;

  border-top: 1px solid rgb(255 255 255 / 9%);
}

.configuration-heading,
.quantity-area {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 24px;
}

.configuration-heading span,
.quantity-area > span {
  color: #8d8d98;
  font-size: 0.82rem;
}

.configuration-heading strong {
  font-size: 0.87rem;
}

.purchase-colors {
  display: flex;
  margin-top: 18px;
  gap: 13px;
}

.purchase-color {
  width: 40px;
  height: 40px;

  border: 4px solid #050509;
  border-radius: 50%;
  outline: 1px solid rgb(255 255 255 / 18%);

  box-shadow:
    inset 0 2px 4px rgb(255 255 255 / 20%),
    0 6px 16px rgb(0 0 0 / 30%);

  transition:
    transform 0.25s ease,
    outline-color 0.25s ease;
}

.purchase-color:hover {
  transform: translateY(-3px);
}

.purchase-color.active {
  outline: 2px solid #ffffff;
  transform: scale(1.1);
}

.purchase-color:focus-visible {
  outline: 3px solid #ffffff;
  outline-offset: 4px;
}

.quantity-area {
  max-width: 500px;
  margin-top: 34px;
}

.quantity-control {
  display: flex;
  align-items: center;
  gap: 19px;

  padding: 8px;

  border: 1px solid rgb(255 255 255 / 10%);
  border-radius: 999px;

  background: rgb(255 255 255 / 4%);
}

.quantity-control button {
  display: flex;
  width: 34px;
  height: 34px;
  align-items: center;
  justify-content: center;

  border-radius: 50%;
  background: rgb(255 255 255 / 8%);
  color: #ffffff;

  font-size: 1.2rem;
}

.quantity-control button:disabled {
  cursor: not-allowed;
  opacity: 0.3;
}

.quantity-control strong {
  min-width: 20px;
  text-align: center;
}

.price-area {
  display: grid;
  max-width: 500px;
  margin-top: 34px;
  grid-template-columns: 1fr 1fr;
  gap: 20px;
}

.price-area > div {
  display: flex;
  flex-direction: column;
  gap: 8px;

  padding: 20px;

  border: 1px solid rgb(255 255 255 / 9%);
  border-radius: 18px;

  background: rgb(255 255 255 / 3%);
}

.price-area span {
  color: #858591;
  font-size: 0.72rem;
}

.price-area strong {
  font-size: 1.25rem;
}

.installment-text {
  max-width: 500px;
  margin-top: 14px;

  color: #70707b;
  font-size: 0.72rem;
}

.add-button {
  width: 100%;
  max-width: 500px;
  margin-top: 26px;
  padding: 18px 24px;

  border-radius: 999px;

  background: linear-gradient(
    135deg,
    #806fff,
    #4b34ff
  );

  color: #ffffff;
  font-weight: 800;

  transition:
    transform 0.25s ease,
    box-shadow 0.25s ease;
}

.add-button:hover {
  transform: translateY(-3px);

  box-shadow:
    0 14px 34px rgb(79 52 255 / 28%);
}

.add-button:focus-visible {
  outline: 3px solid #ffffff;
  outline-offset: 4px;
}

.added-message {
  min-height: 22px;
  margin-top: 15px;

  color: #72e3ad;
  font-size: 0.78rem;
}

@media (max-width: 1000px) {
  .purchase-section {
    grid-template-columns: 1fr;
  }

  .purchase-preview {
    order: 2;
  }
}

@media (max-width: 600px) {
  .purchase-section {
    gap: 50px;
    padding: 100px 24px;
  }

  .price-area {
    grid-template-columns: 1fr;
  }
}
</style>