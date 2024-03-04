<template>
  <b-container id="app" class="container">
    <h1>Deneme</h1>
    <b-row class="mb-4">
      <b-col class="col-md-6">
        <b-card class="card shadow">
          <div class="card-body">
            <h5 class="card-title">Brand Seçin</h5>
            <select
              v-model="selectedBrand"
              class="form-select"
              @change="updateCampaigns"
            >
              <option value="">Select Brand</option>
              <option v-for="brand in uniqueBrands" :key="brand" :value="brand">
                {{ brand }}
              </option>
            </select>
          </div>
        </b-card>
      </b-col>
      <b-col class="col-md-6">
        <b-card class="card shadow">
          <div class="card-body">
            <h5 class="card-title">Campaign Seçin</h5>
            <select
              v-model="selectedCampaign"
              class="form-select"
              :disabled="!selectedBrand"
            >
              <option value="">Select Campaign</option>
              <option
                v-for="campaign in axiosData.campaignStatus.campaign"
                :key="campaign"
                :value="campaign"
              >
                {{ campaign }}
              </option>
            </select>
          </div>
        </b-card>
      </b-col>
    </b-row>
    <b-row class="row mb-4">
      <b-col class="col-md-12">
        <div class="card shadow">
          <div class="card-body">
            <h5 class="card-title">Table</h5>
            <b-table striped hover :items="items" :fields="fields"></b-table>
          </div>
        </div>
      </b-col>
    </b-row>
    <b-row class="row">
      <b-col class="col-md-6">
        <b-card class="card shadow">
          <div class="card-body">
            <h5 class="card-title">Line Chart</h5>
            <apexchart
              :options="lineChartOptions"
              :series="lineChartSeries"
              type="line"
              class="line-chart"
            />
          </div>
        </b-card>
      </b-col>
      <b-col class="col-md-6">
        <b-card class="card shadow">
          <div class="card-body">
            <h5 class="card-title">Bar Chart</h5>
            <apexchart
              :options="barChartOptions"
              :series="barChartSeries"
              type="bar"
              class="bar-chart"
            />
          </div>
        </b-card>
      </b-col>
    </b-row>
  </b-container>
</template>

<script>
import axios from "axios";
import VueApexCharts from "vue-apexcharts";

export default {
  name: "App",
  components: {
    apexchart: VueApexCharts,
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
        chart: {
          id: "line-chart",
        },
        xaxis: {
          categories: [],
        },
      },
      lineChartSeries: [],
      barChartOptions: {
        chart: {
          id: "bar-chart",
        },
        xaxis: {
          categories: [],
        },
      },
      barChartSeries: [],
    };
  },
  created() {
    this.loadData();
  },
  methods: {
    loadData() {
      axios
        .get(this.url)
        .then((response) => {
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
    updateCampaigns() {
      this.selectedCampaign = "";
    },
    updateItems() {
      if (!this.axiosData.campaignStatus) return;

      const { campaignStatus } = this.axiosData;
      const numCampaigns = campaignStatus.brand.length;

      this.items = [];

      for (let i = 0; i < numCampaigns; i++) {
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

        this.items.push(item);
      }
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
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.chart-container {
  display: flex;
  justify-content: space-around;
  margin-top: 20px;
}

.chart {
  width: 100%;
}
.card {
  border: none;
  border-radius: 10px;
  transition: all 0.3s ease;
}

.card:hover {
  transform: translateY(-5px);
}

.form-select {
  background-color: #fff;
  border: 1px solid #ced4da;
  border-radius: 5px;
}

.card-title {
  color: #333;
  font-weight: bold;
}

.line-chart,
.bar-chart {
  width: 100%;
  height: 300px;
  border-radius: 10px;
}

.line-chart path,
.bar-chart path {
  transition: all 0.3s ease;
}

.line-chart:hover path,
.bar-chart:hover path {
  stroke-width: 3px;
}
</style>
