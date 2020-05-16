<template>
  <div class="small">
    <h2 class="title">Taller de integración WebSocket app</h2>
    <h3 class="subtitle">Tarea 3</h3>
    <button @click="toggleConnection()" id="toggle-button">{{this.stateConnection()}}</button>
    <Exchange id="exchange" :state="state" :data-collection="datacollection"></Exchange>
  </div>
</template>

<script>
import Exchange from "./components/Exchange.vue";
import io from "socket.io-client";

var socket = io("wss://le-18262636.bitzonte.com", { path: "/stocks" });

export default {
  data() {
    return {
      state: true,
      stockValues: {},
      dateValues: [],
      datacollection: null
    };
  },
  components: {
    Exchange
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
    fillData() {
      let datasets = [];
      Object.keys(this.stockValues).forEach(stock =>
        datasets.push({
          label: stock,
          backgroundColor: this.stockValues[stock].color,
          borderColor: this.stockValues[stock].color,
          data: this.stockValues[stock].values,
          fill: false
        })
      );
      this.datacollection = {
        labels: this.dateValues,
        datasets: datasets
      };
    },
    toggleConnection() {
      if (this.state) {
        socket.close();
      } else {
        socket.connect();
      }
      this.state = !this.state;
    },
    stateConnection() {
      return this.state ? "On" : "Off";
    },
    getColor() {
      var letters = "0123456789ABCDEF";
      var color = "#";
      for (var i = 0; i < 6; i++) {
        color += letters[Math.floor(Math.random() * 16)];
      }
      return color;
    },
    fetchExchanges() {
      socket.emit("EXCHANGES", null);
      socket.on("EXCHANGES", data => {
        console.log("Exchanges:\t", data);
        //   const time = new Date(data.time).toLocaleString("es-CL");
        //   if (!(data.ticker in this.stockValues)) {
        //     this.stockValues[data.ticker] = {
        //       color: this.getColor(),
        //       values: [data.value],
        //       time: [time]
        //     };
        //   } else {
        //     this.stockValues[data.ticker].values.push(data.value);
        //     this.stockValues[data.ticker].time.push(time);
        //   }
        //   if (!this.dateValues.includes(time)) {
        //     this.dateValues.push(time);
        //   }
        //   this.fillData();
      });
    },
    fetchStocks() {
      socket.emit("STOCKS", null);
      socket.on("STOCKS", data => {
        console.log("Stocks:\t", data);
        //   const time = new Date(data.time).toLocaleString("es-CL");
        //   if (!(data.ticker in this.stockValues)) {
        //     this.stockValues[data.ticker] = {
        //       color: this.getColor(),
        //       values: [data.value],
        //       time: [time]
        //     };
        //   } else {
        //     this.stockValues[data.ticker].values.push(data.value);
        //     this.stockValues[data.ticker].time.push(time);
        //   }
        //   if (!this.dateValues.includes(time)) {
        //     this.dateValues.push(time);
        //   }
        //   this.fillData();
      });
    },
    fetchUpdate() {
      socket.on("UPDATE", data => {
        console.log("Update:\t", data);
        const time = new Date(data.time).toLocaleString("es-CL");
        if (!(data.ticker in this.stockValues)) {
          this.stockValues[data.ticker] = {
            color: this.getColor(),
            values: [data.value],
            time: [time],
            buy: [],
            sell: []
          };
        } else {
          this.stockValues[data.ticker].values.push(data.value);
          this.stockValues[data.ticker].time.push(time);
        }
        if (!this.dateValues.includes(time)) {
          this.dateValues.push(time);
        }
        this.fillData();
      });
    },
    fillBuy() {},
    fetchBuy() {
      socket.on("BUY", data => {
        console.log("Buy:\t", data);
        const time = new Date(data.time).toLocaleString("es-CL");
        if (!(data.ticker in this.stockValues)) {
          this.stockValues[data.ticker] = {
            color: this.getColor(),
            values: [],
            time: [time],
            buy: [data.value],
            sell: []
          };
        } else {
          this.stockValues[data.ticker].buy.push(data.value);
          this.stockValues[data.ticker].time.push(time);
        }
        if (!this.dateValues.includes(time)) {
          this.dateValues.push(time);
        }
        this.fillBuy();
      });
    },
    fetchSell() {
      socket.on("SELL", data => {
        console.log("Sell:\t", data);
        const time = new Date(data.time).toLocaleString("es-CL");
        if (!(data.ticker in this.stockValues)) {
          this.stockValues[data.ticker] = {
            color: this.getColor(),
            values: [],
            time: [time],
            buy: [],
            sell: [data.value]
          };
        } else {
          this.stockValues[data.ticker].sell.push(data.value);
          this.stockValues[data.ticker].time.push(time);
        }
        if (!this.dateValues.includes(time)) {
          this.dateValues.push(time);
        }
        this.fillData();
      });
    },
    fetchData() {
      if (this.state) {
        this.fetchUpdate();
        this.fetchBuy();
        this.fetchSell();
      }
    }
  }
};
</script>

<style>
.small {
  max-width: 1000px;
  margin: 50px auto;
}
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
