<template>
<div>
    <header>
        <h1>Welcome to Simons Burgers!</h1>
        <img src="https://cdn.jwplayer.com/v2/media/UE3wsRB7/thumbnails/sAfXooUi.jpg?width=1280" id="header-image">
    </header>
    <main>
        <section>
            <h2>Pick your burger</h2>
            <p>This is where you choose what burger you want </p>
        </section>
        <section id="burgers">
          <Burger
            v-for="burger in burgers"
            v-bind:burger="burger"
            v-bind:key="burger.name"
            v-on:orderedBurger="addToOrder($event)"
          />
        </section>
        <section id="delivery-information">
            <h2>
                Delivery information
            </h2>
            <form>
                <p>
                    <label for="firstlastname">Name</label><br>
                    <input type="text" id="firstlastname" v-model="name" required="required" placeholder="First- and Last name">
                </p>
<p>Klicka vart på kartan vi ska leverera</p>
<div id="kartlåda">
  <div id="karta" v-on:click="setLocation">
    <div id="dots">
      <div v-bind:style="{ left: location.x + 'px', top: location.y + 'px' }">
        T
      </div>
    </div>
  </div>
</div>
                 <p>
                    <label for="email">E-mail</label><br>
                    <input type="text" id="email" v-model="email" required="required" placeholder="E-mail">
                 </p>
                 <p>
                    <label for="payment">Payment method</label><br>
                    <select id="payment" v-model="pay">
                        <option>Swish</option>
                        <option>Cash</option>
                        <option>Card</option>
                        <option>Doing the dishes</option>
                    </select>
                 </p>
            <p>
                Select your gender
            </p>
            <label for="female">Female</label>
                <input type="radio" id="female" v-model="gender" value="Female" required="required">
            <label for="male">Male</label>
                <input type="radio" id="male" v-model="gender" value="Male" required="required">
            <label for="other">Other</label>
                <input type="radio" id="other" v-model="gender" value="other" required="required">

    </form>
    <button type="button" v-on:click="placeOrder">
        <img src="https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcQpJAtgogXvF-W29_azLr5gDjgfnvlgiRhfOGkZu_7SEg&s=10" style="width:130px; height: 130px;">
    </button>
        </section>
        <section>
            <h3>About us</h3>
            <p>Our adress is Dag Hammarskjölds väg 3</p>
        </section>
    </main>
    <footer>
        <hr>
        All rights reserved.
    </footer>
</div>
</template>

<script>
import Burger from '../components/OneBurger.vue'
import io from 'socket.io-client'

const socket = io("localhost:3000");
function MenuItem(name, url, kCal, gluten, lactose) {
  this.name = name;
  this.url = url;
  this.kCal = kCal;
  this.gluten = gluten;
  this.lactose = lactose;
}

const myBurgers = [
  new MenuItem("Truffle Burger","https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcQDdlxcjzoOwhyWrqZg8DMTV6Ki5lQHoFLX5qHzA8kCrA&s", 500, true, true),
  new MenuItem("Standard Burger", "https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcS5aMdcNJ23DHijcsKKj1XIsarkuJlGYx9BBJbdLGiReQ&s=10", 450, true, false),
  new MenuItem("Vegan burger", "https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcQHmLq9fLWln8Rqh-p9w8LslhQ0J_3aHyuQTSUSMU7eVg&s=10", 350, false, false)
];

console.log(myBurgers);

export default {
  name: 'HomeView',
  components: {
    Burger
  },
  data: function () {
    return {
      burgers: myBurgers,
      orderedBurger: {},
      name: '',
      email: '',
      pay:'Swish',
      gender:'Female',
      location: {x:0, y:0}
    }
  },
  methods: {

    addToOrder: function (event) {
      this.orderedBurger[event.name] = event.amount;
    },

    placeOrder: function() {
      console.log(
        this.name,
        this.email,
        this.pay,
        this.gender,
        this.orderedBurger
      );

      socket.emit("addOrder", {
        orderId: this.getOrderNumber(),
        details: {
          x: this.location.x,
          y: this.location.y
        },
        orderItems: this.orderedBurger,
        customerdetails: {
          name: this.name,
          email: this.email,
          pay: this.pay,
          gender: this.gender
        }
      });
    },
    getOrderNumber: function () {
      return Math.floor(Math.random()*100000);
    },
    setLocation: function (event) {
      var offset = {x: event.currentTarget.getBoundingClientRect().left,
                    y: event.currentTarget.getBoundingClientRect().top};
                                this.location = { 
                                            x: event.clientX - 10 - offset.x,
                                           y: event.clientY - 10 - offset.y };
    },
  }
}
</script>

<style>

#kartlåda {
  width: 100%;
  height: 400px;
  overflow: scroll;
}
  #karta {
    position: relative;
    width: 1920px;
    height: 1078px;
    background: url("/img/polacks.jpg")
  }

#dots div {
  position: absolute;
  background: black;
  color: white;
  border-radius: 10px;
  width: 20px;
  height: 20px;
  text-align: center;
}

@import url('https://fonts.googleapis.com/css2?family=Agbalumo&family=Cormorant:wght@700&display=swap');
body {
    font-family: 'Agbalumo', sans-serif;
    font-size: 12pt;
}

p {
    color: red;
}

h1 {
    font-family: 'Times New Roman', Times, serif;
    font-size: 36pt;
}
main, header, footer, nav ul {
    max-width: 60rem;
    margin: 20px auto 0 auto;
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
    position: relative;
    overflow: hidden;    
    width: 100%;
    height: 200px;
}

#header-image {
    width: 100%;
    height: auto;
    opacity: 0.5;
}


button  {
    margin: 20px;
}

header h1 {
    position: absolute;
    top:40px;
    left: 0;
    right: 0;
    text-align: center;
    padding: 10px;
    width:40rem;
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

.highlight-ingredients {
    color: white;
    font-weight: bold;
}

#burgers p {
    color: white;
}

#burgers {
    display: grid;
    grid-template-columns: 33% 33% 33%;
    gap: 15px;
    background-color: black;
    color: white;
    border: 3px dotted white;
}

#burgers img {
    width: 100%;
    height: 200px;
}

#burgers div {
    margin: 15px;
    padding: 15px;
}

#delivery-information {
    border: 3px solid red;
    padding: 10px

}

button:hover {
    background-color: green;
    cursor: pointer;
}
</style>