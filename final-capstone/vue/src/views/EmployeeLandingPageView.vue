<template>
  <div class="employee-landing-page">
      <h1>Welcome, BeefCakes employee!</h1>
      <div class="emp-landing-display">
      <router-link v-bind:to="{ name: 'employee-products' }" class="emp-landing-card"><img src="images/products.png"/><p>Products</p></router-link><br />
      <router-link v-bind:to="{ name: 'employee-orders' }" class="emp-landing-card"><img src="images/orders.png"/><p>Orders</p></router-link>
      </div>
      </div>
</template>

<script>
export default {

}
</script>

<style>
.employee-landing-page {
  margin-top: 40px;
}

.emp-landing-display {
  margin-top: 20px;
  border: 5px double rgba(0, 0, 0, 0.192);
  display: flex;
  align-items: center;
  justify-content: space-evenly;
  padding: 20px;
  border-radius: 100px;
  background: rgba(221, 220, 221, 0.315);
  -webkit-backdrop-filter: blur(8px);
	backdrop-filter: blur(8px);
}

.emp-landing-card {
  background: rgba(245, 245, 245, 0.486);
  padding: 26px;
  border: 9px dashed rgba(112, 1, 66, 0.178);
  border-radius: 65px;
}

.emp-landing-card img {
  opacity: 65%;
}  

</style>