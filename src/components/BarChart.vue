<template>
  <b-card class="card shadow">
    <div class="card-body">
      <h5 class="card-title">Bar Chart</h5>
      <apexchart
        type="bar"
        :options="chartOptions"
        :series="chartSeries"
        class="bar-chart"
      />
    </div>
  </b-card>
</template>

<script>
import VueApexCharts from "vue-apexcharts";

export default {
  components: {
    apexchart: VueApexCharts,
  },
  props: ["chartOptions", "chartSeries"],
};
</script>
