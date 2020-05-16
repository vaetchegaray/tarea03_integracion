<template>
  <div class="container">
    <Chart :chart-data="this.dataCollection"></Chart>
  </div>
</template>

    <script>
import Chart from "@/components/Chart";

export default {
  name: "Exchange",
  components: { Chart },
  props: ["dataCollection", "state"],
  data() {
    return {};
  },
  methods: {}
};
</script>


<style scoped>
</style>