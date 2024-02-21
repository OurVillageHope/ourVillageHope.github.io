<template>
  <div class="EchartContain">
    <div class="EchartBox" ref="refEchart" />
  </div>
</template>
<script>
// 引入 echarts 核心模块，核心模块提供了 echarts 使用必须要的接口。
import * as echarts from "echarts";

export default {
  name: "ChartView",
  props: {
    chartOption: {
      type: Object,
      required: true,
    },
  },
  data() {
    return {
      myEchart: null,
    };
  },
  watch: {
    chartOption: {
      deep: true,
      handler(newVal) {
        this.setOptions(newVal);
      },
    },
  },
  mounted() {
    this.initChart();
    window.addEventListener("resize", this.resizeHandler);
  },
  beforeDestroy() {
    if (!this.myEchart) {
      return;
    }
    window.removeEventListener("resize", this.resizeHandler);
    this.myEchart.dispose();
    this.myEchart = null;
  },
  methods: {
    resizeHandler() {
      this.myEchart.resize();
    },
    initChart() {
      this.myEchart = echarts.init(this.$refs.refEchart);
      this.myEchart.setOption(this.chartOption);
    },
    setOptions(option) {
      this.clearChart();
      this.resizeHandler();
      if (this.myEchart) {
        this.myEchart.setOption(option);
      }
    },
    clearChart() {
      this.myEchart && this.myEchart.clear();
    },
  },
};
</script>
<style scoped>
.EchartContain {
  width: 100%;
  height: 100%;
}
.EchartBox {
  width: 100%;
  height: 100%;
}
</style>
