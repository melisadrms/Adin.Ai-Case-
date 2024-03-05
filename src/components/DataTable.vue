<template>
  <b-col class="col-md-12">
    <div class="card shadow">
      <div class="card-body">
        <h5 class="card-title">Table</h5>
        <b-table striped hover :items="items" :fields="fields">
          <template #cell(effectivenessTrend)="data">
            <b-icon
              :icon="getTrendIcon(data.value)"
              class="trend-icon"
              :style="{ backgroundColor: getBackgroundColor(data.value) }"
            />
          </template>
          <template #cell(mediaTrend)="data">
            <b-icon
              :icon="getTrendIcon(data.value)"
              class="trend-icon"
              :style="{ backgroundColor: getBackgroundColor(data.value) }"
            />
          </template>
          <template #cell(creativeTrend)="data">
            <b-icon
              :icon="getTrendIcon(data.value)"
              class="trend-icon"
              :style="{ backgroundColor: getBackgroundColor(data.value) }"
            />
          </template>
          <template #cell(growthTrend)="data">
            <b-icon
              :icon="getTrendIcon(data.value)"
              class="trend-icon"
              :style="{ backgroundColor: getBackgroundColor(data.value) }"
            />
          </template>
          <template #cell(socialTrend)="data">
            <b-icon
              :icon="getTrendIcon(data.value)"
              class="trend-icon"
              :style="{ backgroundColor: getBackgroundColor(data.value) }"
            />
          </template>
          <template #cell(effectiveness)="data">
            <div>{{ data.value }}</div>
          </template>
          <template #cell(media)="data">
            <div>{{ data.value }}</div>
          </template>
          <template #cell(creative)="data">
            <div>{{ data.value }}</div>
          </template>
          <template #cell(growth)="data">
            <div>{{ data.value }}</div>
          </template>
          <template #cell(social)="data">
            <div>{{ data.value }}</div>
          </template>
        </b-table>
      </div>
    </div>
  </b-col>
</template>

<script>
export default {
  props: ["items", "fields"],
  methods: {
    getTrendIcon(value) {
      if (value === 1) return "graph-up";
      else if (value === -1) return "graph-down";
      else return "compass";
    },
    getBackgroundColor(value) {
      if (value === 1) return "green";
      else if (value === -1) return "red";
      else return "gray";
    },
  },
};
</script>

<style>
.trend-icon {
  font-size: 1.2rem;
  border-radius: 50%;
  padding: 0.2rem;
}
</style>
