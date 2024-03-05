<template>
  <div>
    <b-card class="card shadow">
      <div class="card-body">
        <h5 class="card-title">{{ title }}</h5>
        <select
          v-model="selectedItem"
          class="form-select"
          @change="updateSelectedItem"
        >
          <option value="">{{ placeholder }}</option>
          <option v-for="item in items" :key="item" :value="item">
            {{ item }}
          </option>
        </select>
      </div>
    </b-card>
  </div>
</template>

<script>
export default {
  props: {
    title: String,
    placeholder: String,
    items: Array,
    selectedBrand: String,
    value: String,
  },
  data() {
    return {
      selectedItem: this.value || "",
    };
  },
  watch: {
    value(newValue) {
      console.log("Value prop changed:", newValue);
      this.selectedItem = newValue;
      console.log("Selected Item:", this.selectedItem);
    },
  },
  methods: {
    updateSelectedItem() {
      this.$emit("update:selectedItem", this.selectedItem);
    },
  },
};
</script>

<style>
.card {
  border: none;
  border-radius: 10px;
  transition: all 0.3s ease;
}
.card-title {
  color: #333;
  font-weight: bold;
}
.card:hover {
  transform: translateY(-5px);
}
</style>
