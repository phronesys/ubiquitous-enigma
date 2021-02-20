<template>
  <div id="app">
    <div id="chart" style="width: 640px; height: 480px"></div>
    <div class="docs-frappe" @click="openDocs">Frappe Docs</div>
  </div>
</template>

<script>
import { Chart } from "frappe-charts/dist/frappe-charts.esm.js";
import "frappe-charts/dist/frappe-charts.min.css";
import axios from "./axios";

export default {
  name: "App",
  data: () => ({
    labels: [
      "enero",
      "febrero",
      "marzo",
      "abril",
      "mayo",
      "junio",
      "julio",
      "agosto",
      "septiembre",
      "octubre",
      "noviembre",
      "diciembre",
    ],
    datasets: [
      {
        name: "Abandoned",
        type: "bar",
        values: [25, 40, 30, 35, 8, 52, 17, -4],
      },
      {
        name: "Answered",
        type: "bar",
        values: [25, 50, -10, 15, 18, 32, 27, 14],
      },
      {
        name: "Transferred",
        type: "bar",
        values: [25, 50, -10, 15, 18, 32, 27, 14],
      },
    ],
    recivedDataset: [],
  }),
  mounted() {
    axios
      .get("https://api.jsonbin.io/b/5e975f3d2940c704e1d8be6a")
      .then((res) => {
        // console.log(res);
        // this.recivedDataset = res.data;
        let monthIndex = 1;
        const answered = {
          name: "Answered",
          type: "bar",
          values: [],
        };
        const abandoned = {
          name: "Abandoned",
          type: "bar",
          values: [],
        };
        const transferred = {
          name: "Transferred",
          type: "bar",
          values: [],
        };
        for (let i = 0; i < 12; i++) {
          const month = res.data.filter((j) => j.month === monthIndex);
          // console.log(month[0]); // aca quedan ordenados los meses
          // we create 3 objects here to add them to the dataset

          answered.values.push(month[0].answered);

          abandoned.values.push(month[0].abandoned);

          transferred.values.push(month[0].transferred);

          monthIndex++;
        }
        console.log(answered);
        this.recivedDataset.push(answered);
        this.recivedDataset.push(abandoned);
        this.recivedDataset.push(transferred);
        const el = document.querySelector("#chart");
        const chart = new Chart(el, {
          // or a DOM element,
          title: "My Awesome Chart",
          data: { labels: this.labels, datasets: this.recivedDataset },
          type: "bar", // or 'bar', 'line', 'scatter', 'pie', 'percentage'
          height: el.clientWidth,
          colors: ["#7cd6fd", "#743ee2", "#96509c"],
        });

        this.datasets = this.recivedDataset;
      })
      .catch((error) => {
        console.error(error);
      });
  },
  methods: {
    openDocs() {
      window.open("https://frappe.io/charts/docs", "_blank");
    },
  },
};
</script>

<style>
html,
body,
#app {
  width: 100%;
  height: 100%;
  margin: 0;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.docs-frappe {
  width: auto;
  display: flex;
  position: absolute;
  top: 10px;
  left: 10px;
  color: #3199d8;
  text-decoration: underline;
  cursor: pointer;
}
</style>
