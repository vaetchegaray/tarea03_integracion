<template>
  <div class="small">
    <button @click="toggleConnnection">{{ button.state }}</button>
    <line-chart :chart-data="datacollection" id="mychart"></line-chart>
  </div>
</template>

<script>
import LineChart from "../../LineChart.js";
import io from "socket.io-client";

var socket = io("wss://le-18262636.bitzonte.com", { path: "/stocks" });

var stockValues = {};

export default {
  components: {
    LineChart,
  },
  data() {
    return {
      datacollection: {
        datasets: [],
        labels: [],
      },
      button: {
        state: true,
      },
    };
  },
  mounted() {
    this.button.state = true;
    this.getRealtimeData();
  },
  methods: {
    fillData(fetchedata) {
      const time = new Date(fetchedata.time).toLocaleString("es-CL");
      const label = fetchedata.ticker;
      const value = fetchedata.value;

      if (!(label in stockValues)) {
        stockValues[label] = [
          {
            value: value,
            time: time,
            color: this.getColor(),
          },
        ];
      } else {
        const stock = stockValues[fetchedata.ticker];
        const check = stock.filter((stock) => stock.time == time);
        if (check.length == 0) {
          stock.push({
            value: value,
            time: time,
            color: stock[0].color,
          });
        }
      }
      this.constructDataset();
      console.log(stockValues);
    },
    getColor() {
      var letters = "0123456789ABCDEF";
      var color = "#";
      for (var i = 0; i < 6; i++) {
        color += letters[Math.floor(Math.random() * 16)];
      }
      return color;
    },
    constructDataset() {
      const labels = [];
      const datasets = [];
      for (var label in stockValues) {
        if (!(stockValues[label].time in labels)) {
          labels.push(stockValues[label].time);
          datasets.push({
            data: stockValues[label].value,
            label: label,
            backgroundColor: stockValues[label].color,
          });
        }
      }
      this.datacollection = {
        labels: labels,
        datasets: [],
      };
    },
    getRealtimeData() {
      socket.on("UPDATE", (data) => {
        console.log("Update:\t", data);
        this.fillData(data);
      });
    },
    toggleConnnection() {
      if (this.button.state) {
        socket.off();
      } else {
        this.getRealtimeData();
      }
      this.button.state = !this.button.state;
    },
  },
};
</script>

<style></style>
