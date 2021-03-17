<template>
  <div id="app" class="ui container">
    <Pricing />
  </div>
</template>

<script>
import Pricing from "./components/Pricing";

export default {
  name: "App",
  components: {
    Pricing,
  },
};
</script>

<style>
#app {
  font-family: "Inter", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
