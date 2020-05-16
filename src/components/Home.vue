<template>
  <div class="hello">
    <div class="container">
      <div class="row">
        <h2 class="title">Taller de integración WebSocket app</h2>
        <h3 class="subtitle">Tarea 3</h3>
        <button @click="toggleConnection()" id="toggle-button">{{this.state}}</button>
        <LineChart :chart-data="datacollection"></LineChart>
      </div>
    </div>
  </div>
</template>

    <script>
import LineChart from "@/components/LineChart";
import io from "socket.io-client";

var socket = io("wss://le-18262636.bitzonte.com", { path: "/stocks" });

var stockValues = {};
var dateValues = [];

export default {
  name: "Home",
  components: { LineChart },
  data() {
    return {
      datacollection: null,
      state: false
    };
  },
  created() {
    this.fetchData();
    this.fillData();
    this.state = true;
  },
  mounted() {
    this.fillData();
  },
  methods: {
    getColor() {
      var letters = "0123456789ABCDEF";
      var color = "#";
      for (var i = 0; i < 6; i++) {
        color += letters[Math.floor(Math.random() * 16)];
      }
      return color;
    },
    fillData() {
      let datasets = [];
      Object.keys(stockValues).forEach(stock =>
        datasets.push({
          label: stock,
          backgroundColor: stockValues[stock].color,
          borderColor: stockValues[stock].color,
          data: stockValues[stock].values,
          fill: false
        })
      );
      this.datacollection = {
        labels: dateValues,
        datasets: datasets
      };
    },
    fetchData() {
      socket.on("UPDATE", data => {
        console.log("Update:\t", data);
        if (!(data.ticker in stockValues)) {
          stockValues[data.ticker] = {
            color: this.getColor(),
            values: [data.value]
          };
        } else {
          stockValues[data.ticker].values.push(data.value);
        }
        const time = new Date(data.time).toLocaleString("es-CL");
        if (!dateValues.includes(time)) {
          dateValues.push(time);
        }
        this.fillData();
      });
    },
    toggleConnection() {
      if (this.state) {
        socket.off();
      } else {
        this.fetchData();
      }
      this.state = !this.state;
    }
  }
};
</script>


    <style scoped>
.title {
  text-align: center;
  margin-top: 40px;
}
.subtitle {
  text-align: center;
}
h1,
h2 {
  font-weight: normal;
}
a {
  color: #42b983;
}
</style>