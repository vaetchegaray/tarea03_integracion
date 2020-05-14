<template>
  <div class="small">
    <line-chart :chart-data="datacollection" id="mychart"></line-chart>
  </div>
</template>

<script>
import LineChart from "../LineChart.js";
import io from "socket.io-client";

var socket = io("wss://le-18262636.bitzonte.com", { path: "/stocks" });

export default {
  components: {
    LineChart
  },
  data() {
    return {
      datacollection: null
    };
  },
  created() {
    this.getRealtimeData();
  },
  methods: {
    fillData(data) {
      this.datacollection = {
        labels: [data, data],
        datasets: [
          {
            label: "Google Stock",
            backgroundColor: "#1A73E8",
            data: [data.value, data.value]
          },
          {
            label: "Microsoft Stock",
            backgroundColor: "#2b7518",
            data: [data.value + 1, data.value + 1]
          }
        ]
      };
    },
    getRealtimeData() {
      socket.on("UPDATE", data => {
        console.log("Update:\t", data);
        this.fillData(data);
      });
    }
  }
};
</script>

<style>
.small {
  max-width: 600px;
  margin: 150px auto;
}
</style>
