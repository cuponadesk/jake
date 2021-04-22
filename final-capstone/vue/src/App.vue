<template>
  <div id="app">
    <div id="nav">
           <!-- Hide these customer-side links if Employee is logged in -->
      <div class="cust-section">
      <router-link v-bind:to="{ name: 'home' }" v-show="!$store.state.token">Home</router-link>
      <span v-show="!$store.state.token">&nbsp;|&nbsp;</span>
      <router-link v-bind:to="{ name: 'cakes' }" v-show="!$store.state.token">Our BeefCakes</router-link>
      <span v-show="!$store.state.token">&nbsp;|&nbsp;</span>
      <router-link v-bind:to="{ name: 'order-cake' }" v-show="!$store.state.token">Order</router-link>
      <span v-show="!$store.state.token">&nbsp;|&nbsp;</span>
      <router-link v-bind:to="{ name: 'shopping-cart' }" v-show="!$store.state.token"><img src="images/beefcakes.png"/>
      <span class="counter" v-show="this.$store.state.currentActiveOrder.itemsInOrder.length">: {{this.$store.state.currentActiveOrder.itemsInOrder.length}}</span></router-link>
      </div>
      <div class="emp-section">
      <router-link v-bind:to="{ name: 'login' }" v-if="!$store.state.token">Employee Login</router-link>

 
      <router-link v-bind:to="{ name: 'employee-landing' }"  v-if="$store.state.token != ''" >Portal</router-link>
      <span v-show="$store.state.token !=''">&nbsp;|&nbsp;</span> 
      <router-link v-bind:to="{ name: 'employee-products' }"  v-if="$store.state.token != ''" >Products</router-link>
      <span v-show="$store.state.token !=''">&nbsp;|&nbsp;</span>
      <router-link v-bind:to="{ name: 'employee-orders' }"  v-if="$store.state.token != ''" >Orders</router-link>
      <span v-show="$store.state.token !=''">&nbsp;|&nbsp;</span>
      <router-link v-bind:to="{ name: 'logout' }" v-if="$store.state.token != ''">Logout</router-link>
      </div>
    </div>
    <router-view class="content" />
  </div>
</template>

<style>
/* Font imports */
@import url('https://fonts.googleapis.com/css2?family=Pacifico&display=swap');
@import url('https://fonts.googleapis.com/css2?family=Quicksand&display=swap');
@import url('https://fonts.googleapis.com/css2?family=Sacramento&display=swap');
@import url('https://fonts.googleapis.com/css2?family=Rye&display=swap');
@import url('https://fonts.googleapis.com/css2?family=Josefin+Slab&display=swap');
@import url('https://fonts.googleapis.com/css2?family=Diplomata&display=swap');
@import url('https://fonts.googleapis.com/css2?family=Roboto+Slab&display=swap');
@import url('https://fonts.googleapis.com/css2?family=Antic+Slab&display=swap');
@import url('https://fonts.googleapis.com/css2?family=Abril+Fatface&display=swap');
@import url('https://fonts.googleapis.com/css2?family=Parisienne&display=swap');
@import url('https://fonts.googleapis.com/css2?family=Lancelot&display=swap');
/* @import url('https://fonts.googleapis.com/css2?family=Comic+Neue:wght@700&display=swap'); */

/* 'Roboto Slab', serif;  'Antic Slab', serif;  'Abril Fatface', cursive; 'Josefin Slab', serif;
   'Parisienne', cursive; 'Lancelot', cursive; 'Comic Neue',  */

html {
  background: repeating-linear-gradient(90deg, #e9feff, #e9feff 50px, #ffb8ea 50px, #ffb8ea 100px);
  min-height:100%;
  margin: 0;
  background-repeat: no-repeat;
  background-attachment: fixed;
}

h1 {
  font-family: 'Rye', cursive;
  font-weight: 500;
  margin-bottom: 1%;
  color:rgb(184, 12, 69);
  font-size: 3.3rem;
}

h3 {
  font-family: 'Parisienne', cursive;
  font-weight: 500;
  font-size: 2rem;
}

p {
  color:rgb(73, 2, 26);
  font-family: 'Josefin Slab', serif;
  font-size: 1.2rem;
  font-weight: 600;
}

.content {
  margin-top: 24px;
}

#nav {
  position: fixed;
  top: 0;
  background-color: rgba(255, 153, 247, 0.616);
  width: 100%;
  height: 35px;
  font-family: 'Josefin Slab', serif;
  font-size: 1.5rem;
  font-weight: 900;
}

#nav a {
  text-decoration: none;
  transition-duration: 0.6s;
}

#nav a:hover {
  background-color: rgba(255, 152, 238, 0.39);
  border-radius: 5px;
  padding: 3px;
  transition-duration: 0.6s;
  font-size: 1.58rem;
}

.cust-section {
  float: none;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  background-color: rgba(173, 243, 255, 0.87);
  border-radius: 5px;
  padding: 5px;
}

.cust-section img {
  margin-bottom: -4px;
  justify-content: center;
  align-items: center;
  opacity: 72%;
}

.emp-section {
  float: right;
  margin-top: 1px;
  margin-right: 1.5%;
  background-color: rgba(173, 243, 255, 0.493);
  border-radius: 5px;
  padding: 5px;
}

@media screen and (max-width: 700px) {
  #nav, .emp-section {
    float: none;
    display: block;
  }
  
  .cust-section {
    position: relative;
    top: 0;
    left: 0;
    transform: none;
  }

  #app {
    margin-top: 68px;
  }

}
</style>
