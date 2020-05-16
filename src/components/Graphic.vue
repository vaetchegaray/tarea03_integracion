<template>
  <div class="small">
    <button @click="toggleConnnection()">{{ button.state }}</button>
    <line-chart :chart-data="datacollection" id="mychart"></line-chart>
  </div>
</template>

<script>
import LineChart from "./LineChart.js";
import io from "socket.io-client";

var socket = io("wss://le-18262636.bitzonte.com", { path: "/stocks" });

var stockValues = {};

export default {
  components: {
    LineChart,
  },
  props: {},
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
      var new_stock = false;
      var dataset_stock = null;

      if (!(label in stockValues)) {
        new_stock = true;
        stockValues[label] = [
          {
            value: value,
            time: time,
            color: this.getColor(),
          },
        ];
        dataset_stock = stockValues[label][0];
      } else {
        const stock = stockValues[fetchedata.ticker];
        const check = stock.filter((stock) => stock.time === time);
        if (check.length == 0) {
          dataset_stock = {
            value: value,
            time: time,
            color: stock[0].color,
          };
          stock.push(dataset_stock);
        }
      }
      this.getDataset(new_stock, dataset_stock, label);
    },
    getColor() {
      var letters = "0123456789ABCDEF";
      var color = "#";
      for (var i = 0; i < 6; i++) {
        color += letters[Math.floor(Math.random() * 16)];
      }
      return color;
    },
    getDataset(new_stock, dataset_stock, label) {
      const labels = this.datacollection.labels;
      const datasets = this.datacollection.datasets;

      if (new_stock && dataset_stock) {
        if (!labels.includes(dataset_stock.time)) {
          labels.push(dataset_stock.time);
        }
        datasets.push({
          backgroundColor: dataset_stock.color,
          label: label,
          data: [dataset_stock.value],
        });
      } else if (dataset_stock) {
        if (!labels.includes(dataset_stock.time)) {
          labels.push(dataset_stock.time);
        }
        const to_push = datasets.find((stock) => stock.label === label);
        to_push.data.push(dataset_stock.value);
      }
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
