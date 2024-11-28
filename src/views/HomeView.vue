<template>
        <header id="header">
            <img id = "image" src = "/img/baga.jpeg" alt="title image">
            <h1 id = "headline">Welcome to Old Daddy Burger Shop</h1>
        </header>
        <main>
            <section id = "burgers">
                <div class = "burgertitle">
                    <h2>Burger Menu</h2>
                    <p>Choose the burger you want!</p>
                </div>
                <div class="menu">
                      <Burger
                        v-for="burger in menu"
                        v-bind:key= "burger.name"
                        v-bind:burger="burger"
                        v-on:orderedBurgers="addToOrder($event)"
                      />
                </div>
            </section>
            <section id = "orderInfo">
                <h3>Customer Information</h3>
                <p>Please fill in your information: </p>
                <h4>Delivery Information</h4>
                <p>
                    <label for="fullname">Full name: </label>
                    <input type="text" id="fullname" required="required" v-model="fn" placeholder="First- and Last name">
                </p>
                <p>
                    <label for="email">E-mail: </label>
                    <input type="email" id="email" required="required" v-model="em" placeholder="E-mail address">
                </p>
                <p>
                    <label for="payment">Payment options: </label>
                    <select id = "payment" name = "crp" v-model="paymentOption">
                        <option selected="selected">Swish</option>
                        <option>visa</option>
                        <option>mastercard</option>
                    </select>
                </p>
                <p>
                    <label for="gender-male">Gender:</label>
                    <input type="radio" id="gender-male" name="gd" value="male" v-model="gender">
                    <label for="gender-male">Male </label>
                    <input type="radio" id="gender-female" name="gd" value="female" v-model="gender">
                    <label for="gender-female">Female </label>
                    <input type="radio" id="do not wish to provide " name="gd" value="do not wish to provide" v-model="gender">
                    <label for="do not wish to provide">Do not wish to provide</label>
                </p>
                <p>Please choose your location: </p>
                <div id="mapScroll">
                  <div id="map" v-on:click="setLocation">
                    <div id="dots">
                      <div v-bind:style="{ left: location.x + 'px', top: location.y + 'px'}">
                        T
                      </div>
                    </div>
                  </div>
                </div>
            </section>
        </main>
        <button type="submit" class="order-button" v-on:click="addOrder">
          <img src="/img/images.png" style="width: 60px; height: 30px;">
            Place my order
        </button>
        <hr>
        <footer>
            @Burger Inc.
        </footer>
</template>

<script>
import Burger from '../components/OneBurger.vue'
import io from 'socket.io-client'
import menu from '../assets/menu.json'

const socket = io("localhost:3000");

function MenuItem(name, imageUrl, kcal, containsGluten, containsLactose) {
    this.name = name;
    this.imageUrl = imageUrl;
    this.kcal = kcal;
    this.containsGluten = containsGluten;
    this.containsLactose = containsLactose;
}

/* menu: [
const menuItem1 = new MenuItem("Spicy Chicken Burger", "/img/burger1.jpg", 300, true, true);
const menuItem2 = new MenuItem("Grilled Chicken Burger", "/img/burger2.jpg", 150, false, false);
const menuItem3 = new MenuItem("Cheese Fish Burger", "/img/burger3.jpg", 150, false, false);]
*/

export default {
  name: 'HomeView',
  components: {
    Burger
  },
  data: function () {
    return {
      menu: menu,
      fn: '',
      em: '',
      orders: [],
      orderedBurgers: {},
      paymentOption: '',
      gender: '',
      location: { x: 0,
            y: 0
          },
    }
  },

  methods: {
    getOrderNumber: function () {
      return Math.floor(Math.random()*100000);
    },
    addToOrder: function (event) {
       if (this.orderedBurgers[event.name]) {
         this.orderedBurgers[event.name] += event.amount;
       } else {
         this.orderedBurgers[event.name] = event.amount;
       }
    },
    computed: {
        formattedBurgerNames() {
            return Object.keys(this.orderedBurgers).join(', ');
        }
    },
    setLocation: function(event){
        const mapRect = event.currentTarget.getBoundingClientRect();
        const clientX = event.clientX;
        const clientY = event.clientY;

        const x = clientX - mapRect.left + event.currentTarget.scrollLeft;
        const y = clientY - mapRect.top + event.currentTarget.scrollTop;

        this.location = { x, y };
    },
    addOrder: function () {
       console.log(this.fn);
       console.log(this.em);
       console.log(this.paymentOption);
       console.log(this.gender);
       console.log(this.location);
       console.log('Burger ordered:', this.orderedBurgers);

       var orderData = {
           orderId: this.getOrderNumber(),
           orderedBurgers: { ...this.orderedBurgers },
           fullName: this.fn,
           email: this.em,
           paymentOption: this.paymentOption,
           gender: this.gender,
           location: this.location
       };
       console.log("Order data to send:", orderData);
       socket.emit('addOrder', orderData);
       this.$set(this, 'orderedBurgers', {});
    }
  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Agbalumo&family=Cormorant:wght@700&display=swap');

#mapScroll {
    position: relative;
    width: auto;
    height: 20em;
    overflow: scroll;
}

#map {
  width: 100%;
  height: 100%;
  background: url("/img/polacks.jpg");
  background-size: contain;
  overflow: scroll;
  cursor: pointer;
  position: relative;
}


body {
    font-family: Times New Roman;
    font-size: 18pt;
}

html{
  font-size: 30px;
}

#header {
  margin:20px 20px 20px 20px;
  height: 200px;
  overflow:hidden;
  position: relative;
}

#image{
  opacity: 0.5;
  width: 100%;
  height: auto;
}

.menu {
    display: flex;
    flex-wrap: wrap;
    gap: 20px;
    justify-content: center;
}



#headline{
  font-size: 30pt;
  position: absolute;
  margin-top: -600px;
  top: 680px;
  left:250px;
  right:0px;
}


.allergen{
    font-weight: bold;
}

#burgers {
  margin:20px 20px 20px 20px;
  padding: 20px;
  background-color: black;
  color: white;
  border: 2px dashed white;
}

#orderInfo{
    margin:20px 20px 20px 20px;
    padding: 20px;
    border: 2px dashed black;
}

.order-button {
    margin: 20px auto;
    text-align: center;
    color: black;
}

.menu-item {
  padding: 20px;
  margin: 20px;
}

button:hover {
    background-color: #4CAF50;
    color: white;
    border: 1px solid #4CAF50;
    cursor: pointer;
    transform: scale(1.05);
    transition: 0.3s;
}

  #dots {
    position: relative;
    margin: 0;
    padding: 0;
    background-repeat: no-repeat;
    width:1920px;
    height: 1078px;
    cursor: crosshair;
    background-image: url('/img/polacks.jpg');
  }

  #dots div {
    position: absolute;
    background: black;
    color: white;
    border-radius: 10px;
    width:20px;
    height:20px;
    text-align: center;
  }


h1 {
    font-family: 'Agbalumo';
    font-size: 25pt;
}

main {
    background-color: white;
}

</style>