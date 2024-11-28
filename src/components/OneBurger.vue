


<template>
    <div class="menu-item a">
        <h1>{{ burger.name }}</h1>
        <img :src = "burger.imageUrl" :alt="burger.name" style="width: 250px; height: 200px;">
            <ul class ="allergy">
                <li>kCal: {{ burger.kcal }}</li>
                <li v-if="burger.lactose">Contains <span class = 'bold'>lactose</span></li>
                <li v-if="burger.gluten">Contains <span class = 'bold'>gluten</span></li>
            </ul>
            <div class = "order">
                <p>Amount ordered: </P>
                    <button @click="decreaseAmount">-</button>
                    <p>{{ amountOrdered }}</p>
                    <button @click="increaseAmount">+</button>
            </div>
    </div>
</template>

<script>
export default {
  name: 'OneBurger',
  props: {
    burger: Object
  },
  data: function(){
    return {
      amountOrdered: 0,
    }
  },
  methods: {
    increaseAmount() {
      this.amountOrdered += 1; // Increment the burger count
      this.$emit('orderedBurgers', {
        name: this.burger.name,
        amount: this.amountOrdered
      })
    },
    decreaseAmount() {
      if (this.amountOrdered > 0) {
        this.amountOrdered -= 1; // Decrement the burger count only if greater than 0
        this.$emit('orderedBurgers', {
          name: this.burger.name,
          amount: this.amountOrdered
        })
      }
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
    .menu-item {
      padding: 20px;
      margin: 20px;
    }
    .allergen {
    font-weight: bold;
    color: red;
    }
    .order{
    display: flex;
    align-items: center;
    gap: 10px;
    }
    .bold{
    font-weight: bold;
    }
    .allergy {
    min-height: 100px;
    margin: 40px;
    padding: 0;
    }

</style>