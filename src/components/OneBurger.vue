<template>
  <div class="burger-card" id="burgarinformation">
    <h3>{{ burger.name }}</h3>
    <img
      v-bind:src="burger.url"
      alt="Brandburgaren"
      style="width: 200px; height: 200px"
    />
    <ul class="ingredients">
      <li>{{ burger.kCal }}</li>
      <li>{{ burger.lactose }}</li>
      <li>{{ burger.gluten }}</li>
    </ul>
    <button v-on:click="decrease">-</button>
    <button v-on:click="increase">+</button>
    <p>Antal beställda: {{ amountOrdered }}</p>
  </div>
</template>

<script>
export default {
  name: "OneBurger",
  props: {
    burger: Object,
  },
  data() {
    return {
      amountOrdered: 0,
    };
  },
  methods: {
    addBurger: function () {
      this.amountOrdered += 1;
      this.$emit("orderedBurger", { name: this.burger.name, amount: this.amountOrdered });
    },
    increase() {
      this.amountOrdered++;
      this.$emit("orderedBurger", { name: this.burger.name, amount: this.amountOrdered });
    },
    decrease() {
      if (this.amountOrdered > 0) {
        this.amountOrdered--;
      };
      this.$emit("orderedBurger", { name: this.burger.name, amount: this.amountOrdered });
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.ingredients {
  color: #ff5500;
  font-weight: bold;
}

#burgarinformation {
  background-color: black;
  color: white;
  border: 2px dashed #ff9900;
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
</style>
