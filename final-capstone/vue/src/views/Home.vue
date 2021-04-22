<template>
  <div class="home">
   <span class="banner"><h1>BeefCakes BakeShop</h1>
      <p>Welcome to BeefCakes BakeShop.</p>
      <p class="second-line-banner">Any day can be cheat day if you want it to be!</p></span>
    <img src=".images/beefcakes.png" />
  </div>
</template>

<script>
export default {
  name: "home"
};
</script>
<style>
.home {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

.banner {
  background: rgba(255, 255, 255, 0.521);
  /* -webkit-backdrop-filter: blur(10px);
	backdrop-filter: blur(10px); */
  display: flex;
  flex-direction: column;
  justify-items: center;
  align-items: center;
  line-height: 20px;
  margin-top: 19px;
  border-radius: 49px;
  border: 6px dotted rgba(252, 73, 103, 0.39);
  padding: 13px;
}

.home img {
  width: 40%;
  border: 11px double rgba(209, 4, 4, 0.212);
  border-radius: 250px;
  opacity: 100%;
}

.home h2 {
  font-family: 'Quicksand';
  font-weight: 400;
  margin-top: -3%;
}

.home p {
  font-size: 1.5rem;
}

.second-line-banner {
  margin-top: -20px;
}

@media screen and (max-width: 700px) {
  h1 {
    font-size: 2.2rem;
  }

  .banner p {
    font-size: 1.1rem;
  }

}
</style>