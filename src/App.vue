<template>
  <div id="app">
    <!-- <ChartTest></ChartTest> -->
    <Csv></Csv>
  </div>
</template>

<script>
import ChartTest from "./views/chartTest/index.vue";
import Csv from "./views/csv/index.vue";


export default {
  name: "App",
  components: {
    ChartTest,
    Csv
  }
};
</script>

<style></style>
