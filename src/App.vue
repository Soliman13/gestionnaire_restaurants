<template>
  <div class="container" id="app">
    <h1>Gestionnaire de restaurants</h1>
    <ListeRestaurants/>
  </div>
</template>

<script>
import ListeRestaurants from "./components/ListeRestaurants.vue";

export default {
  name: "app",
  components: {
    ListeRestaurants
  }
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
