<template>
  <div id="app">
    <div class="chart__block" v-if="charts">
      <div class="chart_item" v-for="(chart, i) in charts" :key="'chart_' + i">
        <p class="chart_item-title">
          {{ chart.title }}
        </p>
        <BaseChart
          :data="chart.data"
          :type="chart.type"
          :is-show-tooltip="chart.isShowTooltip"
        />
      </div>
    </div>
  </div>
</template>

<script>
import BaseChart from "./components/BaseChart.vue";

export default {
  name: "App",
  components: {
    BaseChart,
  },

  data: () => ({
    chartTypes: ["line", "step", "column"],
    chartType: "line",
    chartsData: null,
    chartIsShowTooltip: true,
    charts: null,
  }),

  created() {
    this.loadChartData();
  },

  methods: {
    async loadChartData() {
      const JSON_DATA = await import("./chartData.json");
      this.charts = JSON_DATA.default;
    },
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.chart__block {
  display: flex;
  flex-flow: row wrap;
  gap: 1rem;
}

.chart_item {
  flex-grow: 1;
  border: 1px solid #6395f9;
  box-sizing: border-box;
  width: calc((100% / 2) - (2rem / 2));
  padding: 1rem;
}
</style>
