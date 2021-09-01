<template>
  <div class="chart__container" ref="container"></div>
</template>

<script>
import { Chart } from "@antv/g2";

export default {
  name: "BaseChart",

  props: {
    data: {
      type: Array,
      default: () => [],
    },

    type: {
      type: String,
      default: () => "line",
      validator: function(value) {
        return ["line", "step", "column"].includes(value);
      },
    },

    isShowTooltip: {
      type: Boolean,
      default: () => true,
    },
  },

  data: () => ({
    chart: null,
    geometry: null,
  }),

  watch: {
    data() {
      this.chart.clear();
      this.setParams();
    },

    type() {
      this.chart.clear();
      this.setParams();
    },

    isShowTooltip() {
      this.setTooltip();
    },
  },

  mounted() {
    this.initializeChart();
    this.setParams();
  },

  methods: {
    /**
     * Инициализация диаграммы
     */
    initializeChart() {
      this.chart = new Chart({
        container: this.$refs.container,
        autoFit: true,
        height: 500,
      });
    },

    /**
     * Установка основных параметров
     * и отрисовка
     */
    setParams() {
      const { data, type } = this;

      this.setChartData(data);
      this.setChartScale();
      this.setChartType(type);
      this.setTooltip();

      this.renderChart();
    },

    /**
     * Установка данных или
     * обновление
     *
     * @param {Array} data - данные
     * @param {Boolean} isUpdate - если нужно обновить данные
     */
    setChartData(data, isUpdate = false) {
      if (isUpdate) {
        return this.chart.changeData(data);
      }

      this.chart.data(data);
    },

    setChartScale() {
      this.chart.scale("group", { range: [0, 1] });
      this.chart.scale("value", { nice: true });
    },

    /**
     * Установка геометрии диаграммы
     *
     * @param {String} type - тип диаграммы
     * @param {Boolean} isUpdate - если нужно обновить
     */
    setChartType(type, isUpdate = false) {
      if (isUpdate) this.chart.clear();

      if (type === "line") {
        this.setChartTypeLine();
      } else if (type === "step") {
        this.setChartTypeStep();
      } else if (type === "column") {
        this.setChartTypeColumn();
      }

      if (isUpdate) this.chart.render();
    },

    setChartTypeLine() {
      this.geometry = this.chart.line().position("group*value");

      if (this.isShowTooltip) {
        this.geometry.label("value");
      }
    },

    setChartTypeStep() {
      this.geometry = this.chart
        .line()
        .position("group*value")
        .shape("hv");
    },

    setChartTypeColumn() {
      const { data, chart } = this;

      chart.axis("group", {
        tickLine: {
          alignTick: false,
        },
      });
      chart.axis("value", false);

      this.geometry = chart.interval().position("group*value");
      chart.interaction("element-active");

      data.forEach((item) => {
        chart
          .annotation()
          .text({
            position: [item.type, item.value],
            content: item.value,
            style: {
              textAlign: "center",
            },
            offsetY: -30,
          })
          .text({
            position: [item.type, item.value],
            content: (item.percent * 100).toFixed(0) + "%",
            style: {
              textAlign: "center",
            },
            offsetY: -12,
          });
      });
    },

    setTooltip() {
      if (!this.isShowTooltip) {
        this.chart.tooltip(false);
        return false;
      }

      this.chart.tooltip({
        showCrosshairs: true,
        shared: true,
      });
    },

    renderChart() {
      this.chart.render();
    },
  },
};
</script>
