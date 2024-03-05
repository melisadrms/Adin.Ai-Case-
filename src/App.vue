<template>
  <b-container id="app" class="container">
    <div class="d-flex justify-content-center">
      <SelectFilter
        :title="'Marka Seçin'"
        :placeholder="'Marka Seçin'"
        :items="uniqueBrands"
        v-model="selectedBrand"
        @update:selectedItem="setSelectedBrand"
        class="col-md-6"
      />

      <SelectFilter
        v-if="selectedBrand"
        :title="'Campaign Seçin'"
        :placeholder="'Select Campaign'"
        :items="
          axiosData.campaignStatus ? axiosData.campaignStatus.campaign : []
        "
        v-model="selectedCampaign"
        @update:selectedItem="setSelectedCampaign"
        class="col-md-6"
      />
    </div>
    <DataTable :items="items" :fields="fields" />

    <div class="d-flex justify-content-center">
      <div class="col-md-6">
        <LineChart
          :chart-options="lineChartOptions"
          :chart-series="lineChartSeries"
        />
      </div>
      <div class="col-md-6">
        <BarChart
          :chart-options="barChartOptions"
          :chart-series="barChartSeries"
        />
      </div>
    </div>
  </b-container>
</template>

<script>
import axios from "axios";
import SelectFilter from "./components/SelectFilter.vue";
import DataTable from "./components/DataTable.vue";
import LineChart from "./components/LineChart.vue";
import BarChart from "./components/BarChart.vue";

export default {
  name: "App",
  components: {
    SelectFilter,
    DataTable,
    LineChart,
    BarChart,
  },
  data() {
    return {
      url: "https://ni3mpgik72.execute-api.eu-central-1.amazonaws.com/dev/frontend",
      axiosData: {},
      selectedBrand: "",
      selectedCampaign: "",
      fields: [
        { key: "campaign", label: "Campaign" },
        { key: "brand", label: "Brand" },
        { key: "effectiveness", label: "Effectiveness" },
        { key: "effectivenessTrend", label: "Effectiveness Trend" },
        { key: "media", label: "Media" },
        { key: "mediaTrend", label: "Media Trend" },
        { key: "creative", label: "Creative" },
        { key: "creativeTrend", label: "Creative Trend" },
        { key: "growth", label: "Growth" },
        { key: "growthTrend", label: "Growth Trend" },
        { key: "social", label: "Social" },
        { key: "socialTrend", label: "Social Trend" },
      ],
      items: [],
      lineChartOptions: {
        chart: { id: "line-chart" },
        xaxis: { categories: [] },
      },
      lineChartSeries: [],
      barChartOptions: {
        chart: { id: "bar-chart" },
        xaxis: { categories: [] },
      },
      barChartSeries: [],
    };
  },
  created() {
    this.loadData();
  },

  watch: {
    selectedCampaign(newValue, oldValue) {
      console.log("Selected Campaign changed:", newValue);
      if (newValue !== oldValue) {
        this.updateItems();
      }
    },
  },
  methods: {
    loadData() {
      axios
        .get(this.url)
        .then((response) => {
          console.log("API response:", response.data);
          if (response.data && response.data.campaignStatus) {
            this.axiosData = response.data;
            this.updateItems();
            this.updateLineChart();
            this.updateBarChart();
          } else {
            console.error("API verisi beklenen formatta değil");
          }
        })
        .catch((error) => {
          console.error("API veri alınamadı:", error);
        });
    },

    setSelectedBrand(selectedBrand) {
      this.selectedBrand = selectedBrand;
    },

    setSelectedCampaign(selectedCampaign) {
      this.selectedCampaign = selectedCampaign;
    },

    updateItems() {
      if (!this.axiosData.campaignStatus) return;

      const { campaignStatus } = this.axiosData;
      let filteredItems = [];

      if (this.selectedCampaign) {
        const selectedCampaignIndex = campaignStatus.campaign.findIndex(
          (campaign) => campaign === this.selectedCampaign
        );

        if (selectedCampaignIndex !== -1) {
          const item = {
            campaign: campaignStatus.campaign[selectedCampaignIndex] || "",
            brand: campaignStatus.brand[selectedCampaignIndex] || "",
            effectiveness:
              campaignStatus.effectiveness[selectedCampaignIndex] || 0,
            effectivenessTrend:
              campaignStatus.effectivenessTrend[selectedCampaignIndex] || 0,
            media: campaignStatus.media[selectedCampaignIndex] || 0,
            mediaTrend: campaignStatus.mediaTrend[selectedCampaignIndex] || 0,
            creative: campaignStatus.creative[selectedCampaignIndex] || 0,
            creativeTrend:
              campaignStatus.creativeTrend[selectedCampaignIndex] || 0,
            growth: campaignStatus.growth[selectedCampaignIndex] || 0,
            growthTrend: campaignStatus.growthTrend[selectedCampaignIndex] || 0,
            social: campaignStatus.social[selectedCampaignIndex] || 0,
            socialTrend: campaignStatus.socialTrend[selectedCampaignIndex] || 0,
          };

          filteredItems.push(item);
        }
      } else {
        for (let i = 0; i < campaignStatus.brand.length; i++) {
          const item = {
            campaign: campaignStatus.campaign[i] || "",
            brand: campaignStatus.brand[i] || "",
            effectiveness: campaignStatus.effectiveness[i] || 0,
            effectivenessTrend: campaignStatus.effectivenessTrend[i] || 0,
            media: campaignStatus.media[i] || 0,
            mediaTrend: campaignStatus.mediaTrend[i] || 0,
            creative: campaignStatus.creative[i] || 0,
            creativeTrend: campaignStatus.creativeTrend[i] || 0,
            growth: campaignStatus.growth[i] || 0,
            growthTrend: campaignStatus.growthTrend[i] || 0,
            social: campaignStatus.social[i] || 0,
            socialTrend: campaignStatus.socialTrend[i] || 0,
          };

          filteredItems.push(item);
        }
      }

      this.items = filteredItems;
    },
    updateLineChart() {
      const { scoreTrend } = this.axiosData;

      const categories = Object.keys(scoreTrend["Quantum Quest"]);
      const series = Object.entries(scoreTrend).map(([key, value]) => ({
        name: key,
        data: categories.map((date) => value[date] || 0),
      }));

      this.lineChartOptions.xaxis.categories = categories;
      this.lineChartSeries = series;
    },
    updateBarChart() {
      const { scoreTrend } = this.axiosData;

      const categories = Object.keys(scoreTrend["Quantum Quest"]);
      const series = Object.entries(scoreTrend).map(([key, value]) => ({
        name: key,
        data: categories.map((date) => value[date] || 0),
      }));

      this.barChartOptions.xaxis.categories = categories;
      this.barChartSeries = series;
    },
  },
  computed: {
    uniqueBrands() {
      return this.axiosData.campaignStatus
        ? Array.from(new Set(this.axiosData.campaignStatus.brand))
        : [];
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
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.form-select {
  background-color: #fff;
  border: 1px solid #ced4da;
  border-radius: 5px;
}

.card.shadow {
  padding: 0.5rem;
  margin: 0.5rem;
}
</style>
