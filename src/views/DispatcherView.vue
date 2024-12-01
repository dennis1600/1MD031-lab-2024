<template>
  <div id="orders">
    <div id="orderList">
      <div v-for="(order, index) in orders" :key="'order' + index" class="order">
        <h4>#{{ index }}:</h4>

        <p class="order-items">
          {{ Object.entries(order.orderItems)
            .map(([burger, amount]) => `${burger}: ${amount} st`)
            .join(", ") }}
        </p>

        <p class="customer-info">
          {{ order.details.name }}, {{ order.details.email }},
          {{ order.details.paymentMethod }}, {{ order.details.gender }}
        </p>
      </div>
      <button v-on:click="clearQueue" class="clear-button">Clear Queue</button>
    </div>

    <div id="dots">
      <div
        v-for="(order, index) in orders"
        :key="'dots' + index"
        class="dot-container"
        :style="{ left: order.details.x + 'px', top: order.details.y + 'px' }"
      >
        <div class="dot">T</div>
        <div class="order-id">{{ "#" + order.orderId }}</div>
      </div>
    </div>
  </div>
</template>

<script>
import io from "socket.io-client";
const socket = io("http://localhost:3000");

export default {
  name: "DispatcherView",
  data() {
    return {
      orders: null,
    };
  },
  created() {
    socket.on("currentQueue", (data) => {
      this.orders = data.orders;
    });
  },
  methods: {
    clearQueue() {
      socket.emit("clearQueue");
    },
  },
};
</script>

<style scoped>
#orderList {
  top: 1em;
  left: 1em;
  position: absolute;
  z-index: 2;
  color: black;
  background: rgba(255, 255, 255, 0.8);
  padding: 0.5em;
  font-size: 0.85em;
  max-width: 300px;
  max-height: 90vh;
  overflow-y: auto;
  border-radius: 5px;
}

.order {
  margin-bottom: 0.5em;
  padding: 0.5em;
  border-bottom: 1px solid #ccc;
}

.order h4 {
  margin: 0;
  font-size: 1em;
  font-weight: bold;
}

.order-items {
  margin: 0.2em 0;
  font-style: italic;
  color: #333;
}

.customer-info {
  margin: 0.2em 0;
  font-size: 0.9em;
  color: #555;
}


#dots {
  position: relative;
  width: 1920px;
  height: 1078px;
  cursor: crosshair;
  background-image: url("/img/polacks.jpg");
  background-size: cover;
}

.dot-container {
  position: absolute;
  transform: translate(-50%, -50%);
}

.dot {
  position: absolute;
  background: black;
  color: white;
  border-radius: 50%;
  width: 25px;
  height: 25px;
  line-height: 25px;
  font-size: 14px;
  font-weight: bold;
  z-index: 10;
  top: -10px; 
  left: -10px; 
}


.order-id {
  position: absolute;
  top: 5px; 
  left: 50%;
  transform: translateX(-50%);
  font-size: 30px;
  color: #00aaff;
  font-weight: bold;
  z-index: 5;
}

.clear-button {
  margin-top: 1em;
  padding: 0.5em;
  background-color: #ff4d4d;
  color: white;
  border: none;
  cursor: pointer;
  border-radius: 5px;
  width: 100%;
}

.clear-button:hover {
  background-color: #e60000;
}
</style>
