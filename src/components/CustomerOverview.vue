<template>
  <div>
    <b-row class="text-center">
      <b-col>
        <div
          class="filter-card p-3"
          :class="{
            'bg-active-filter': totalIsActive,
            'bg-light': !totalIsActive,
          }"
          @click="getTotalEmployees"
        >
          <h6>Total Empleados</h6>
          <h4>
            <strong>{{ totalCustomers }}</strong>
          </h4>
        </div>
      </b-col>
    </b-row>
  </div>
</template>

<script>
export default {
  name: "CustomerOverview",
  props: {
    totalCustomers: Number,
    activeCustomers: Number,
  },
  data() {
    return {
      totalIsActive: true,
      activeIsActive: false,
    };
  },
  methods: {
    getTotalEmployees() {
      this.totalIsActive = true;
      this.activeIsActive = false;
      this.$emit("totalCustomersIsActive");
    },
    activeCustomersIsActiveFilter() {
      this.totalIsActive = false;
      this.activeIsActive = true;
      this.$emit("activeCustomerIsActive");
    },
  },
};
</script>

<style>
.filter-card:hover {
  cursor: pointer;
  text-decoration: underline;
}

.bg-active-filter {
  background-color: #e9f1fe;
  color: #074297;
}
</style>
