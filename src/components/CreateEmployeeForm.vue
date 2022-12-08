<template>
  <b-form class="mt-3">
    <b-row>
      <b-row>
        <h5 class="text-secondary">Detalle del empleado</h5>
      </b-row>
      <br>
      <b-col cols="6">
        <b-form-group id="fullname" label="Nombre Completo:" label-for="fullname">
          <b-form-input
            id="fullname"
            type="text"
            placeholder="Nombre Completo"
            v-model="employee.fullname"
          ></b-form-input>
        </b-form-group>
      </b-col>
      <b-col cols="6">
        <b-form-group id="department" label="Departamento" label-for="department">
          <b-form-select id="department" v-model="employee.department" class="form-select">
            <b-form-select-option value="contabilidad">Contabilidad</b-form-select-option>
            <b-form-select-option value="finanzas">Finanzas</b-form-select-option>
            <b-form-select-option value="produccion">Produccion</b-form-select-option>
          </b-form-select>
        </b-form-group>
      </b-col>
    </b-row>
    <b-row class="mt-4">
      <h5 class="text-secondary">Detalle del vehiculo</h5>
    </b-row>
    <b-row>
      <b-col cols="6">
        <b-form-group
          id="type"
          label="Tipo de vehiculo"
          label-for="type"
        >
          <b-form-select id="type" v-model="employee.vehicle.type" class="form-select">
            <b-form-select-option value="moto">Motocicleta</b-form-select-option>
            <b-form-select-option value="automovil">Automovil</b-form-select-option>
          </b-form-select>
        </b-form-group>
      </b-col>
      <b-col cols="6">
        <b-form-group
          id="licensePlate"
          label="Numero de placa"
          label-for="licensePlate"
        >
          <b-form-input
            id="licensePlate"
            type="text"
            placeholder="Numero de placa"
            v-model="employee.vehicle.licensePlate"
          ></b-form-input>
        </b-form-group>
      </b-col>
      <b-col cols="6">
        <b-form-group id="color" label="Color:" label-for="color">
          <b-form-input
            id="color"
            type="text"
            placeholder="Color"
            v-model="employee.vehicle.color"
          ></b-form-input>
        </b-form-group>
      </b-col>
    </b-row>
    
    <b-row class="mt-4">
      <b-col cols="6">
        <b-button variant="primary" class="px-5" @click="addNewEmployee">Crear empleado</b-button>
      </b-col>
      <b-col cols="6">
        <b-button variant="warning" @click="triggerClose">Cerrar</b-button>
      </b-col>
    </b-row>
  </b-form>
</template>

<script>
import axios from "axios";

export default {
  name: "CreateEmployeeModal",
  data() {
    return {
      employee: {
        vehicle: {
        }
      }
    };
  },
  methods: {
    triggerClose() {
      this.$emit("closeCreateModal");
    },
    addNewEmployee() {
      axios
        .post("http://localhost:3000/employees/", this.employee)
        .then((response) => {
          console.log(response.data);
          this.$emit("closeCreateModal");
          this.$emit("reloadDataTable");
          this.$emit("showSuccessAlert");
        })
        .catch((error) => {
          console.log(error);
        });
    },
  },
};
</script>
