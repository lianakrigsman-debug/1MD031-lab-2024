<template>
  <header>
    <h1>Välkommen till BurgerOnline</h1>
  </header>

  <div id="burgarinformation">
    <h2>Våra burgare</h2>
    <h3>Välj din burgare</h3>
    <div class="wrapper">
      <Burger
        v-for="burger in burgers"
        v-bind:burger="burger"
        v-bind:key="burger.name"
        v-on:orderedBurger="addToOrder($event)"
      />
      {{ orderedBurgers }}
    </div>
  </div>
  <div class="kontakt-karta">
    <div id="kontakt">
      <h2>Kundinformation</h2>

      <div>Fyll i dina preferenser</div>

      <h2>Beställningsinformation</h2>

      <form>
        <p>
          <label for="name">Namn:</label><br />
          <input
            v-model="name"
            type="text"
            id="namn"
            required="required"
            placeholder="För- och efternamn"
          />
          {{ name }}
        </p>
        <p>
          <label for="email">E-mail:</label><br />
          <input
            v-model="eMail"
            type="email"
            id="email"
            required="required"
            placeholder="E-postadress"
          />
          {{ eMail }}
        </p>
        <p>
          <label for="gata">Gata:</label><br />
          <input
            v-model="street"
            type="text"
            inputmode="numeric"
            id="gata"
            required="required"
            placeholder="Gatnamn"
          />
          {{ street }}
        </p>
        <p>
          <label for="gatnummer">Gatnummer:</label><br />
          <input
            v-model="this.streetNumber"
            type="number"
            id="gatnummer"
            required="required"
            placeholder="Gatnummer"
          />
          {{ streetNumber }}
        </p>
      </form>

      <form>
        <p>
          <label for="betalbetod">Betalmetod</label>
          <select id="betalbetod" v-model="rcp">
            <option selected="selected">Kort</option>
            <option>Faktura</option>
            <option>Kontant</option>
            <option>Inte alls</option>
          </select>
          {{ rcp }}
        </p>
      </form>

      <div>Kön</div>

      <form>
        <p>
          <input type="radio" id="man" v-model="gender" value="Man" />
          <label for="man">Man</label>
        </p>
        <p>
          <input type="radio" id="kvinna" v-model="gender" value="Kvinna" />
          <label for="kvinna">Kvinna</label>
        </p>
        <p>
          <input type="radio" id="icke-binär" v-model="gender" value="Icke-binär" />
          <label for="icke-binär">Icke-binär</label>
        </p>
        <p>
          <input type="radio" id="annat" v-model="gender" value="Annat" />
          <label for="annat">Annat</label>
        </p>
      </form>
      {{ gender }}
      <button v-on:click="allInfoClick" type="submit">
        <img src="/public/img/cykel.jpg" style="width: 30px" />
        Skicka order
      </button>
      {{ allInfo }}
    </div>

    <div id="karta">
      <div id="map" v-on:click="addOrder">
        <div
          v-bind:style="{ left: this.location.x + 'px', top: this.location.y + 'px' }"
        ></div>

        click here

        {{ location }}
      </div>
    </div>
  </div>
  <!-- 
    <div id="dots">
      <div
        v-for="(order, key) in orders"
        v-bind:style="{ left: order.details.x + 'px', top: order.details.y + 'px' }"
        v-bind:key="'dots' + key"
      >
        {{ key }}
      </div>
    </div>
 -->

  <footer>
    <p>Copyright © 2025 BurgerOnline</p>
  </footer>
</template>

<script>
import Burger from "../components/OneBurger.vue";
import io from "socket.io-client";
import menu from "/src/assets/menu.json";
import { ref } from "vue";

const socket = io("localhost:3000");

function MenuItem(name, kCal, url, lactose, gluten) {
  this.name = name;
  this.kCal = kCal;
  this.url = url;
  this.lactose = lactose;
  this.gluten = gluten;
}

const burgersArray = [
  new MenuItem("Barger", 300, "/public/img/Brandburgare.jpg", false, true),
  new MenuItem("Birger", 400, "/public/img/donken.jpg", true, true),
  new MenuItem("Berger", 500, "/public/img/gurkburgaren.jpg", false, false),
];

console.log(burgersArray);

export default {
  name: "HomeView",
  components: {
    Burger,
  },
  data: function () {
    return {
      burgers: menu,
      name: "",
      eMail: "",
      street: "",
      streetNumber: "",
      gender: "",
      rcp: "",
      allInfo: [],
      orderedBurgers: {},
      location: { x: 100, y: 100 },
    };
  },
  methods: {
    allInfoClick: function () {
      this.allInfo = [
        "Namn: " + this.name,
        "E-mail: " + this.eMail,
        "Gata: " + this.street,
        "Gatnummer: " + this.streetNumber,
        "Kön: " + this.gender,
        "Betalmetod: " + this.rcp,
      ];
    },
    addToOrder: function (event) {
      this.orderedBurgers[event.name] = event.amount;
      console.log(this.orderedBurgers);
    },
    getOrderNumber: function () {
      return Math.floor(Math.random() * 100000);
    },
    addOrder: function (event) {
      var offset = {
        x: event.currentTarget.getBoundingClientRect().left,
        y: event.currentTarget.getBoundingClientRect().top,
      };
      socket.emit("addOrder", {
        orderId: this.getOrderNumber(),
        details: { x: event.clientX - 10 - offset.x, y: event.clientY - 10 - offset.y },
        orderItems: ["Beans", "Curry"],
      });
    },
  },
};
</script>

<style>
@import url("https://fonts.googleapis.com/css2?family=Agbalumo&family=Cormorant:wght@700&display=swap");
body {
  font-size: 12pt;
  font-family: "Times New Roman", Times, serif;
  color: brown;
}
#map {
  width: 1920px;
  height: 1078px;
  background: url("/img/polacks.jpg");
  cursor: crosshair;
  position: relative;
}
p {
  color: red;
}

h1 {
  font-family: "Alibri", sans-serif;
  font-size: 36pt;
  padding-top: 30px;
}
main,
header,
footer,
nav ul {
  max-width: 40rem;
  margin: 0 auto 0 auto;
}
main {
  background-color: bisque;
}

/* nav ul li {
    display: inline-block;
    background-color: grey;
    padding: 1em;
    margin: 1em;
} */

header {
  background-image: url("../img/polacks.jpg");
  background-size: cover;
  overflow: hidden;
  width: 100%;
  height: 200px;
  opacity: 0.5;
}

header h1 {
  width: 40rem;
  margin: 0 auto;
  text-align: center;
}

nav ul {
  display: grid;
  grid-template-columns: repeat(auto-fill, 9.25em);
  gap: 1em;
  padding: 0;
}

nav li {
  display: block;
  background-color: grey;
  padding: 1em;
}

.Very-good {
  color: green;
}

.Master {
  color: green;
  font-weight: bold;
}
.ingredients {
  color: #ff5500;
  font-weight: bold;
}

#burgarinformation {
  background-color: magenta;
  color: white;
  border: 2px dashed #ff9900;
}

#kontakt {
  border: 2px dashed red;
  background-color: beige;
  padding-left: 20px;
}

button:hover {
  background-color: black;
  color: green;
}
section {
  margin-left: 20px;
  padding-left: 20px;
  margin-right: 20px;
}
button {
  margin-left: 20px;
}
#burgarinformation * div {
  margin-left: 100px;
  padding: 5px;
}
.wrapper {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  justify-content: space-around;
  flex-wrap: wrap;
  gap: 20px;
}
.kontakt-karta {
  display: grid;
  grid-template-columns: 1fr 2fr; /* två kolumner */
  height: 700px;
}
#karta {
  overflow: scroll;
}

#map div {
  position: absolute;
  background: black;
  color: white;
  border-radius: 10px;
  width: 20px;
  height: 20px;
  text-align: center;
}
</style>
