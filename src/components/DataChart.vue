<template>
  <Bar
    :chart-options="chartOptions"
    :chart-data="chartData"
    :chart-id="chartId"
    :dataset-id-key="datasetIdKey"
    :plugins="plugins"
    :css-classes="cssClasses"
    :styles="styles"
    :width="width"
    :height="height"
  />
</template>

<script>
import { Bar } from "vue-chartjs";

import {
  Chart as ChartJS,
  Title,
  Tooltip,
  Legend,
  BarElement,
  CategoryScale,
  LinearScale,
} from "chart.js";

ChartJS.register(
  Title,
  Tooltip,
  Legend,
  BarElement,
  CategoryScale,
  LinearScale
);

export default {
  name: "BarChart",
  components: {
    Bar,
  },
  props: {
    chartId: {
      type: String,
      default: "bar-chart",
    },
    datasetIdKey: {
      type: String,
      default: "label",
    },
    width: {
      type: Number,
      default: 400,
    },
    height: {
      type: Number,
      default: 400,
    },
    cssClasses: {
      default: "",
      type: String,
    },
    styles: {
      type: Object,
      default: () => {},
    },
    plugins: {
      type: Array,
      default: () => [],
    },
    todayData: {
      type: Object,
    },
    yesterdayData: {
      type: Object,
    },
    previousdayData: {
      type: Object,
    },
    dateArray: Array,
  },
  watch: {
    todayData(newV) {
      console.log("Todays data", newV);
    },
  },
  data() {
    return {
      chartOptions: {
        responsive: true,
        maintainAspectRatio: false,
      },
      //   todaysData: {},
      //   yesterdaysData: {},
      //   previousdaysData: {},
    };
  },
  computed: {
    chartData() {
      return {
        labels: [this.dateArray[0], this.dateArray[1], this.dateArray[2]],
        datasets: [
          {
            label: "Pending",
            backgroundColor: "#f13e17",
            data: [
              this.todayData.pending,
              this.yesterdayData.pending,
              this.previousdayData.pending,
            ],
          },
          {
            label: "Completed",
            backgroundColor: "#1aa128",
            data: [
              this.todayData.completed,
              this.yesterdayData.completed,
              this.previousdayData.completed,
            ],
          },
        ],
      };
    },
  },
  //   computed: {
  //     tPending() {
  //       return this.todayData.pending;
  //     },
  //     yPending() {
  //       return this.yesterdayData.pending;
  //     },
  //     pPending() {
  //       return this.previousdayData.pending;
  //     },
  //     tCompleted() {
  //       return this.todayData.completed;
  //     },
  //     yCompleted() {
  //       return this.yesterdayData.completed;
  //     },
  //     pCompleted() {
  //       return this.previousdayData.completed;
  //     },
  //   },
};
</script>
