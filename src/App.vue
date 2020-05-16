<template>
  <div class="small">
    <Home id="home"></Home>
  </div>
</template>

<script>
import Home from "./components/Home.vue";

export default {
  components: {
    Home
  }
};
</script>

<style>
.small {
  max-width: 1000px;
  margin: 50px auto;
}
</style>
