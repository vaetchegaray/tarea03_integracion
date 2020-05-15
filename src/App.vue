<template>
  <div class="small">
    <Graphic :chart-data="datacollection" id="graph"></Graphic>
  </div>
</template>

<script>
import Graphic from "./components/Graphic.vue";

export default {
  components: {
    Graphic,
  },
};
</script>

<style>
.small {
  max-width: 600px;
  margin: 150px auto;
}
</style>
