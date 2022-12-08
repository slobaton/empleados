<template>
  <div>
    <b-row class="mt-2 mb-3">
      <h6 class="text-secondary">
        Estás seguro de quere eliminar a este empleado?
      </h6>
    </b-row>
    <b-row class="mt-2 mb-3">
      <p class="text-danger">
        Esta acción no es reversible y puede resultar en la pérdida si los datos importantes
      </p>
    </b-row>
    <b-row class="mt-4">
      <b-col>
        <b-button variant="danger" @click="removeEmployeeFromData"
          >Eliminar empleado</b-button
        >
        <b-button variant="warning" @click="triggerClose">Cerrar</b-button>
      </b-col>
    </b-row>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "DeleteEmployeeModal",
  props: {
    employeeId: Number,
  },
  methods: {
    triggerClose() {
      this.$emit("closeDeleteModal");
    },
    removeEmployeeFromData() {
      axios
        .delete(`http://localhost:3000/employees/${this.employeeId}`)
        .then(() => {
          this.$emit("reloadDataTable");
          this.$emit("showDeleteAlert");
          this.$emit("closeDeleteModal");
        })
        .catch((error) => {
          console.log(error);
        });
    },
  },
};
</script>
