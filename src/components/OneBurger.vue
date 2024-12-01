<template>
  <div class="burger-item">
    <h3>{{ burger.name }}</h3>
    <img :src="burger.url" alt="Bild på {{ burger.name }}" />
    <p>Kcal: {{ burger.kCal }}</p>
    <p>Innehåller:</p>
    <ul>
      <li v-if="burger.lactose">Laktos</li>
      <li v-if="burger.gluten">Gluten</li>
      <li v-if="burger.fisk">Fisk</li>
      <li v-if="burger.kött">Kött</li>
    </ul>
    <div class="order-controls">
      <button @click="decreaseOrder">-</button>
      <span>{{ amountOrdered }}</span>
      <button @click="increaseOrder">+</button>
    </div>
  </div>
</template>

<script>
export default {
  name: "OneBurger",
  props: {
    burger: {
      type: Object,
      required: true,
    },
  },
  data() {
    return {
      amountOrdered: 0,
    };
  },
  methods: {
    increaseOrder() {
      this.amountOrdered++;
      this.$emit("orderedBurger", {
        name: this.burger.name,
        amount: this.amountOrdered,
      });
    },
    decreaseOrder() {
      if (this.amountOrdered > 0) {
        this.amountOrdered--;
        this.$emit("orderedBurger", {
          name: this.burger.name,
          amount: this.amountOrdered,
        });
      }
    },
  },
};
</script>

<style scoped>
.burger-item {
  border: 2px dashed black;
  padding: 20px;
  background-color: #ffffff;
  text-align: center;
  border-radius: 5px;
}

img {
  max-width: 200px;
  height: auto;
  border-radius: 5px;
  margin-bottom: 10px;
}

ul {
  list-style: none;
  padding: 0;
}

li {
  font-weight: bold;
  color: rgb(0, 0, 0); 
}

.order-controls {
  display: flex;
  justify-content: center;
  align-items: center;
  margin-top: 10px;
}

button {
  background-color: #00aaff;
  color: white;
  border: none;
  padding: 5px 10px;
  cursor: pointer;
  border-radius: 5px;
  margin: 0 10px;
}

button:hover {
  background-color: #458ea0;
}
</style>
