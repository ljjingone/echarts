<template>
  <div id="app">
	<Head/>  
   <router-view></router-view>
  </div>
</template>

<script>
import Head from "./components/Head";
export default {
  name: "App",
  components: {
    Head
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
