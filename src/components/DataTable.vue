<template>
  <div>
    <b-row>
      <b-alert v-model="showSuccessAlert" variant="success" dismissible>
        {{ alertMessage }}
      </b-alert>
    </b-row>
    <b-row>
      <employee-overview
        :totalEmployees="numberOfEmployees"
      ></employee-overview>
    </b-row>
    <b-row class="mt-3">
      <b-card>
        <b-row align-h="between">
          <b-col cols="6">
            <h3>{{ tableHeader }}</h3>
          </b-col>
          <b-col cols="2">
            <b-row>
              <b-col>
                <b-button
                  variant="primary"
                  id="show-btn"
                  @click="showCreateModal"
                >
                  <b-icon-plus class="text-white"></b-icon-plus>
                  <span class="h6 text-white">Agregar empleado</span>
                </b-button>
              </b-col>
            </b-row>
          </b-col>
        </b-row>
        <b-row class="mt-3">
          <b-table
            striped
            hover
            :items="items"
            :fields="fields"
            class="text-center"
          >
            <template #cell(acciones)="data">
              <b-row>
                <b-col cols="7">
                  <b-icon-pencil-square
                    class="action-item"
                    variant="primary"
                    @click="getRowData(data.item.id)"
                  ></b-icon-pencil-square>
                </b-col>
                <b-col cols="1">
                  <b-icon-trash-fill
                    class="action-item"
                    variant="danger"
                    @click="showDeleteModal(data.item.id)"
                  ></b-icon-trash-fill>
                </b-col>
              </b-row>
            </template>
          </b-table>
        </b-row>
      </b-card>
    </b-row>

    <b-modal
      ref="create-employee-modal"
      size="xl"
      hide-footer
      title="Registro de nuevo empleado"
    >
      <create-employee-form
        @closeCreateModal="closeCreateModal"
        @reloadDataTable="getEmployeeData"
        @showSuccessAlert="showAlertCreate"
      ></create-employee-form>
    </b-modal>

    <!-- UPDATE -->
    <b-modal
      ref="edit-employee-modal"
      size="xl"
      hide-footer
      title="Actualizar empleado"
    >
      <edit-employee-form
        @closeEditModal="closeEditModal"
        @reloadDataTable="getEmployeeData"
        @showSuccessAlert="showAlertUpdate"
        :employeeId="employeeId"
      ></edit-employee-form>
    </b-modal>

    <!-- DELETE -->
    <b-modal
      ref="delete-employee-modal"
      size="md"
      hide-footer
      title="Confirm Deletion"
    >
      <delete-employee-modal
        @closeDeleteModal="closeDeleteModal"
        @reloadDataTable="getEmployeeData"
        @showDeleteAlert="showDeleteSuccessModal"
        :employeeId="employeeId"
      >
      </delete-employee-modal>
    </b-modal>
  </div>
</template>

<script>
import axios from "axios";
import EmployeeOverview from "@/components/EmployeeOverview.vue";
import CreateEmployeeForm from "@/components/CreateEmployeeForm.vue";
import EditEmployeeForm from "@/components/EditEmployeeForm.vue";
import DeleteEmployeeModal from "@/components/DeleteEmployeeModal.vue";

export default {
  components: {
    EmployeeOverview,
    CreateEmployeeForm,
    EditEmployeeForm,
    DeleteEmployeeModal,
  },
  data() {
    return {
      fields: [
        {
          key: "fullname",
          label: "Nombre completo",
          sortable: false,
        },
        {
          key: "department",
          label: "Departamento",
          sortable: false,
        },
        {
          key: "vehicle.type",
          label: "Tipo Vehiculo",
          sortable: false,
        },
        {
          key: "vehicle.licensePlate",
          label: "Numero de placa",
          sortable: false,
        },
        "acciones",
      ],
      items: [],
      numberOfEmployees: 0,
      employeeId: 0,
      tableHeader: "",
      showSuccessAlert: false,
      alertMessage: "",
    };
  },
  mounted() {
    this.getEmployeeData();
  },
  methods: {
    showCreateModal() {
      this.$refs["create-employee-modal"].show();
    },
    closeCreateModal() {
      this.$refs["create-employee-modal"].hide();
    },
    getEmployeeData() {
      axios
        .get("http://localhost:3000/employees/")
        .then((response) => {
          this.tableHeader = "Empleados de la empresa";
          this.items = response.data;
          this.numberOfEmployees = response.data.length;
        })
        .catch((error) => {
          console.log(error);
        });
    },
    getRowData(id) {
      this.$refs["edit-employee-modal"].show();
      this.employeeId = id;
    },
    closeEditModal() {
      this.$refs["edit-employee-modal"].hide();
    },
    showAlertCreate() {
      this.showSuccessAlert = true;
      this.alertMessage = "El empleado fue creado con éxito!";
    },
    showAlertUpdate() {
      this.showSuccessAlert = true;
      this.alertMessage = "El empleado fue actualizado con éxito";
    },
    showDeleteModal(id) {
      this.$refs["delete-employee-modal"].show();
      this.employeeId = id;
    },
    closeDeleteModal() {
      this.$refs["delete-employee-modal"].hide();
    },
    showDeleteSuccessModal() {
      this.showSuccessAlert = true;
      this.alertMessage = "El empleado fue eliminado exitosamente!";
    },
  },
};
</script>

<style>
.action-item:hover {
  cursor: pointer;
}
</style>
