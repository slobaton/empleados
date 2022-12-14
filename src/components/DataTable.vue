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
          <b-col cols="12">
            <h3>{{ tableHeader }}</h3>
          </b-col>
          <b-col cols="12">
            <b-row>
              <b-col>
                <b-form-input
                  v-model="searchText"
                  placeholder="Buscar por nombre de empleado"
                ></b-form-input>
              </b-col>
              <b-col>
                <b-button
                  variant="primary"
                  id="show-btn"
                  @click="clearFilter"
                  :disabled="!searchText"
                >
                  <b-icon-x class="text-white"></b-icon-x>
                </b-button>
                <b-button
                  variant="secondary"
                  id="show-btn"
                  @click="filterByName"
                  :disabled="!searchText"
                >
                  <b-icon-search class="text-white"></b-icon-search>
                </b-button>
              </b-col>
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
          <b-col class="mt-3">
            <b-row>
              <b-form-checkbox v-model="checkedMoto" name="check-button" switch @change="check('moto')">
                Motocicleta
              </b-form-checkbox>
              <b-form-checkbox v-model="checkedCar" name="check-button" switch @change="check('automovil')">
                Autom??vil
              </b-form-checkbox>
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
      title="Confirmaci??n de eliminaci??n"
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
      searchText: "",
      checkedMoto: true,
      checkedCar: true,
    };
  },
  mounted() {
    this.getEmployeeData();
  },
  methods: {
    check(type) {
      console.log(type == 'moto');
      console.log(this.checkedMoto);
      switch (type) {
        case 'moto':
            if (!this.checkedMoto) {
              const filterByMoto = this.items.filter(
                item => item.vehicle.type.toLowerCase() != type.toLowerCase()
              );
              this.items = filterByMoto;
            } else {
              this.getFilterData();
            }
          break;
          case 'automovil':
            if (!this.checkedCar) {
              const filterByCar = this.items.filter(
                item => item.vehicle.type.toLowerCase() != type.toLowerCase()
              );
              this.items = filterByCar;
            } else {
              this.getFilterData();
            }
          break;
      }

    },
    getFilterData() {
      axios
        .get("http://localhost:3000/employees/")
        .then((response) => {
          const items = response.data;
          const filterByVehicle = items.filter(item => {
            if (!this.checkedCar) {
              return item.vehicle.type.toLowerCase() != 'automovil'
            }

            if (!this.checkedMoto) {
              return item.vehicle.type.toLowerCase() != 'moto'
            }
            return item;
          });
          this.items = filterByVehicle;
        })
        .catch((error) => {
          console.log(error);
        });
    },
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
      this.alertMessage = "El empleado fue creado con ??xito!";
    },
    showAlertUpdate() {
      this.showSuccessAlert = true;
      this.alertMessage = "El empleado fue actualizado con ??xito";
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
    filterByName() {
      const filterEmployees = this.items.filter(
        item => item.fullname.toLowerCase().includes(this.searchText.toLowerCase())
      );

      console.log(filterEmployees);
      this.items = filterEmployees;
    },
    clearFilter() {
      this.getEmployeeData();
      this.searchText = ""
    }
  },
};
</script>

<style>
.action-item:hover {
  cursor: pointer;
}
</style>
