<template>
  <div>
    <b-row class="text-center">
      <b-col>
        <div
          class="filter-card p-3"
          :class="{'bg-active-filter': true}"
        >
          <h6>Total Empleados</h6>
          <h4>
            <strong>{{ totalEmployees }}</strong>
          </h4>
        </div>
      </b-col>
    </b-row>
  </div>
</template>

<script>
export default {
  name: "EmployeeOverview",
  props: {
    totalEmployees: Number,
  }
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
