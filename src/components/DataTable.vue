<template>
  <div>
    <b-row>
      <b-alert v-model="showSuccessAlert" variant="success" dismissible>
        {{ alertMessage }}
      </b-alert>
    </b-row>
    <b-row>
      <customer-overview
        :totalCustomers="numberOfCustomers"
        :activeCustomers="activeCustomers"
        @totalCustomersIsActive="setFilterTotalIsActive"
        @activeCustomerIsActive="setFilterActiveIsActive"
      ></customer-overview>
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
      ref="create-customer-modal"
      size="xl"
      hide-footer
      title="Registro de nuevo empleado"
    >
      <create-employee-form
        @closeCreateModal="closeCreateModal"
        @reloadDataTable="getCustomerData"
        @showSuccessAlert="showAlertCreate"
      ></create-employee-form>
    </b-modal>

    <!-- UPDATE -->
    <b-modal
      ref="edit-customer-modal"
      size="xl"
      hide-footer
      title="Actualizar empleado"
    >
      <edit-employee-form
        @closeEditModal="closeEditModal"
        @reloadDataTable="getCustomerData"
        @showSuccessAlert="showAlertUpdate"
        :employeeId="employeeId"
      ></edit-employee-form>
    </b-modal>

    <!-- DELETE -->
    <b-modal
      ref="delete-customer-modal"
      size="md"
      hide-footer
      title="Confirm Deletion"
    >
      <delete-employee-modal
        @closeDeleteModal="closeDeleteModal"
        @reloadDataTable="getCustomerData"
        @showDeleteAlert="showDeleteSuccessModal"
        :employeeId="employeeId"
      >
      </delete-employee-modal>
    </b-modal>
  </div>
</template>

<script>
import axios from "axios";
import CustomerOverview from "@/components/CustomerOverview.vue";
import CreateEmployeeForm from "@/components/CreateEmployeeForm.vue";
import EditEmployeeForm from "@/components/EditEmployeeForm.vue";
import DeleteEmployeeModal from "@/components/DeleteEmployeeModal.vue";

export default {
  components: {
    CustomerOverview,
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
      numberOfCustomers: 0,
      activeCustomers: 0,
      activeCustomersData: [],
      employeeId: 0,
      companySearchTerm: "",
      tableHeader: "",
      showSuccessAlert: false,
      alertMessage: "",
    };
  },
  mounted() {
    this.getCustomerData();
  },
  methods: {
    showCreateModal() {
      this.$refs["create-customer-modal"].show();
    },
    closeCreateModal() {
      this.$refs["create-customer-modal"].hide();
    },
    getCustomerData() {
      axios
        .get("http://localhost:3000/employees/")
        .then((response) => {
          this.tableHeader = "Empleados de la empresa";
          this.items = response.data;
          this.numberOfCustomers = response.data.length;
          this.activeCustomersData = response.data.filter(
            (item) => item.customer_status === "active"
          );
          this.activeCustomers = this.activeCustomersData.length;
        })
        .catch((error) => {
          console.log(error);
        });
    },
    getRowData(id) {
      this.$refs["edit-customer-modal"].show();
      this.employeeId = id;
    },
    closeEditModal() {
      this.$refs["edit-customer-modal"].hide();
    },
    setFilterTotalIsActive() {
      this.tableHeader = "Total Customers";
      this.getCustomerData();
    },
    setFilterActiveIsActive() {
      this.tableHeader = "Active Customers";
      this.items = this.activeCustomersData;
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
      this.$refs["delete-customer-modal"].show();
      this.employeeId = id;
    },
    closeDeleteModal() {
      this.$refs["delete-customer-modal"].hide();
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
