<template>
  <div>
  <h2> 
    {{ burger.name }} 
  </h2>
  <img v-bind:src="burger.url" style="width: 200px; height: 200px;">
  <p>
  Ingredients:
  </p>
  <ul>
    <li><span class="highlight-ingredients">{{ burger.kCal }} kCal</span></li>
    <li v-if="burger.gluten"><span class="highlight-ingredients">Gluten</span></li>
    <li v-if="burger.lactose"><span class="highlight-ingredients">Lactose</span></li>
  </ul>
  <div>
  <p> Amount ordered: {{amountOrdered}} </p>
  <button type="button" v-on:click="decreaseOrder">-</button>
  <button type="button" v-on:click="increaseOrder">+</button>
  </div>
  </div>
</template>

<script>
export default {
  name: 'OneBurger',
  props: {
    burger: Object
  },
  data: function() {
    return { amountOrdered: 0,
  }
},
methods: {
  increaseOrder: function () {
    this.amountOrdered += 1;
    this.$emit('orderedBurger', {
      name: this.burger.name,
      amount: this.amountOrdered
    });
  },
  decreaseOrder: function () {
    if (this.amountOrdered > 0) {
      this.amountOrdered -= 1;
      this.$emit('orderedBurger', {
        name: this.burger.name,
        amount: this.amountOrdered
      });
    }
  }
}
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>