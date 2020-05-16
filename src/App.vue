<template>
  <div class="small">
    <Exchange id="exchange"></Exchange>
  </div>
</template>

<script>
import Exchange from "./components/Exchange.vue";

export default {
  components: {
    Exchange
  }
};
</script>

<style>
.small {
  max-width: 1000px;
  margin: 50px auto;
}
</style>
