<template>
  <b-card class="card shadow">
    <div class="card-body">
      <h5 class="card-title">Line Chart</h5>
      <apexchart
        type="line"
        :options="chartOptions"
        :series="chartSeries"
        class="line-chart"
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
