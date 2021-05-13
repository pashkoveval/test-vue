<template>
  <div id="app">
    <Header />
    <Home />
  </div>
</template>

<script>
import Home from "./components/Home.vue";
import Header from "./components/Header.vue";

export default {
  name: "App",
  components: {
    Home,
    Header,
  },

};
</script>

<style>
@import url(https://fonts.googleapis.com/css?family=Lato:100,100italic,300,300italic,regular,italic,700,700italic,900,900italic);
* {
  font-family: lato, sans-serif;
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
</style>
