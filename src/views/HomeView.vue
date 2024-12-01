<template>
  <header>
    <h1>Välkommen till Dennis Burgare</h1>
  </header>

  <section id="burgare">
    <h2>Val av burgare:</h2>
    <p>Här under väljer du burgare:</p>
    <div class="burger-items">
      <Burger
        v-for="(burger, index) in menu"
        :key="burger.name"
        :burger="burger"
        @orderedBurger="addToOrder"
      />
    </div>
  </section>

  <section id="kundinformation">
    <h2>Kundinformation</h2>
    <form>

      <p>
        <label for="name">Namn:</label>
        <input
          type="text"
          id="name"
          v-model="formData.name"
          required
          placeholder="Skriv in hela ditt namn"
        />
      </p>

      <p>
        <label for="mejl">Mejl:</label>
        <input
          type="email"
          id="mejl"
          v-model="formData.email"
          required
          placeholder="Skriv in din mejladress"
        />
      </p>

      <p>
        <label for="betalmetod">Betalmetod:</label>
        <select id="betalmetod" v-model="formData.paymentMethod">
          <option>Swish</option>
          <option>Bankkort</option>
          <option>Kreditkort</option>
        </select>
      </p>

      <p>
        <label for="kön">Kön:</label>
        <select id="kön" v-model="formData.gender">
          <option>Kvinna</option>
          <option>Man</option>
          <option>Ickebinär</option>
        </select>
      </p>

      <p>
        Klicka vart på kartan du vill beställa till och tryck sedan på gröna knappen nedan för att skicka beställningen.
      </p>

      <div id="map-container">
        <div id="map" v-on:click="setLocation">
          <div
            v-if="location.x !== 0 && location.y !== 0"
            class="target"
            v-bind:style="{ left: location.x + 'px', top: location.y + 'px' }"
          >
            T
          </div>
        </div>
      </div>

      <button type="button" v-on:click="submitOrder">
        <img
          src="/img/greenbutton.png.jpeg"
          alt="Green Button"
          style="width: 50px; height: 50px; object-fit: cover;"
        />
        Skicka beställning
      </button>
    </form>
  </section>

  <footer>
    <p>© 2024 Dennis Burgare Inc.</p>
  </footer>
</template>

<script>
import menu from "../assets/menu.json";
import Burger from "../components/OneBurger.vue";
import io from "socket.io-client";

const socket = io("http://localhost:3000");

function MenuItem(name, imageUrl, kCal, lactose, gluten, fisk, kött) {
  this.name = name;
  this.imageUrl = imageUrl;
  this.kCal = kCal;
  this.lactose = lactose;
  this.gluten = gluten;
  this.fisk = fisk;
  this.kött = kött;
}

export default {
  name: "HomeView",
  components: { Burger },
  data() {
    return {
      menu,
      orderedBurgers: {}, 
      location: { x: 0, y: 0 }, 
      formData: {
        name: "",
        email: "",
        paymentMethod: "Swish",
        gender: "Kvinna",
      },
    };
  },
  methods: {
    addToOrder(event) {
      this.orderedBurgers[event.name] = event.amount;
    },
    setLocation(event) {
      const rect = event.target.getBoundingClientRect();
      this.location.x = event.clientX - rect.left;
      this.location.y = event.clientY - rect.top;
    },
    submitOrder() {
      const order = {
        orderId: Math.random().toString(36).substr(2, 9), // gör ett slumpmässigt ID
        details: {
          x: this.location.x,
          y: this.location.y,
          name: this.formData.name,
          email: this.formData.email,
          paymentMethod: this.formData.paymentMethod,
          gender: this.formData.gender,
        },
        orderItems: this.orderedBurgers,
      };

      socket.emit("addOrder", order);
      console.log("Skickad order:", order);
    },
  },
};
</script>


<style>
header {
  position: relative;
  text-align: center;
  background-image: url("/img/headerbild.png.jpg");
  background-size: cover;
  background-position: center;
  width: 100%;
  height: 30vh;
  color: white;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  overflow: hidden; 
}

header::before {
  content: "";
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-image: url("/img/headerbild.png.jpg");
  background-size: cover;
  background-position: center;
  filter: blur(5px); 
  opacity: 0.9; 
  z-index: 1; 
}

header h1 {
  position: relative;
  z-index: 2; 
  font-family: 'Arial', sans-serif;
  font-size: 48px;
  margin: 0;

}

#burgare {
  border: 2px dashed black;
  margin: 20px auto;
  padding: 20px;
  background-color: #f9f9f9;
}

.burger-items {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  gap: 20px;
}

#kundinformation {
  margin: 20px auto;
  padding: 20px;
  border: 2px dashed black;
  background-color: #00aaff;
}

#map-container {
  width: 100%;
  max-width: 1500px;
  height: 400px;
  overflow: scroll;
  border: 2px solid black;
  margin: 20px auto;
  position: relative;
}


#map {
  background: url("/img/polacks.jpg") no-repeat center center;
  background-size: contain;
  width: 1920px;
  height: 1078px;
  position: relative;
  cursor: crosshair;
}

.target {
  position: absolute;
  background: black;
  color: white;
  border-radius: 50%;
  width: 20px;
  height: 20px;
  text-align: center;
  line-height: 20px;
  font-size: 14px;
  transform: translate(-50%, -50%);
}

button {
  cursor: pointer;
  background-color: rgb(0, 255, 72);
  border: none;
  border-radius: 5px;
  padding: 10px 20px;
  font-size: 16px;
  color: rgb(0, 0, 0);
}

button:hover {
  background-color: rgb(0, 200, 50);
}

button:active {
  background-color: rgb(0, 150, 50);
}

select:focus {
  outline: none;
}
</style>
