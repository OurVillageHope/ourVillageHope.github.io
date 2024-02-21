<template>
  <div>
    <chart-view class="chart-content" :chart-option="chartOpt" height="100%" />
    <chart-view class="chart-content" :chart-option="chartPie" height="100%" />
  </div>
</template>

<script>
export default {
  name: "chartTestView",
  data() {
    return {
      chartOpt: {},
      chartPie: {},
    };
  },
  mounted() {},
  created() {
    this.chartOpt = this.$eChartFn.testBar();
    this.chartPie = this.$eChartFn.testPie();
  },
  methods: {},
  watch: {},
};
</script>

<style scoped>
.chart-content {
  width: 25vw;
  height: 25vh;
  border: 1px solid #000;
  margin: 0 auto;
}
</style>
