<template>
  <div class="content">
    <base-header>
      <div class="align-items-center py-3">
        <card
          class="no-border-card col"
          style="margin-bottom: 0.5rem"
          body-classes="px-0 pb-1 card-bodyTopOpcionesRPagosVehiculoPRoduccionPanelDespachoBusqueda cardSelectRubrosEstadosPagosVehiculoProduccionContainerPanelDespachoBusqueda"
          footer-classes="pb-2"
        >
          <div
            class="cardTextoRPagosVehiculoProduccionPanelDespachoBusqueda"
          >MODULO PROCEDENCIA - SECTOR</div>

          <div
            class="cardSelectRubrosEstadosPagosVehiculoProduccionContainerPanelDespachoBusqueda"
          >
            <div class="buttonCenterEndDerecha">
              <base-button
                icon
                type="primary"
                size="sm"
                @click="readMercadosAll()"
              >
                <span class="btn-inner--icon"
                  ><i class="el-icon-search"></i
                ></span>
              </base-button>
              <!--<download-excel
              class="btn btn-outline-success"
              outline
              :header="headerExcelRPagosVehiculoProduccion"
              :data="mListaSalidasPanelBusqueda"
              :fields="json_fields_excelRPagosVehiculoProduccion"
              :worksheet="WorksheetExcelRPagosVehiculoProduccion"
              :name="FileNameExcelRPagosVehiculoProduccion"
            >
              <span class="btn-inner--icon"
                ><i class="ni ni-collection"></i
              ></span>
              <span class="btn-inner--text"> Excel</span>
            </download-excel>-->
              <base-button
                type="default"
                size="sm"
                v-if="isPermisosActions"
                @click="showAddMercado()"
                title="NUEVO MERCARDO"
              >
                <span class="btn-inner--icon"
                  ><i class="ni ni-fat-add"></i
                ></span>
              </base-button>
            </div>
          </div>
        </card>

        <card
          class="no-border-card"
          style="margin-bottom: 0rem"
          body-classes="card-bodyRPagosVehiculoProduccion px-0 pb-1"
          footer-classes="pb-2"
        >
          <el-table
            element-loading-text="Cargando Datos..."
            v-loading="loadingMercado"
            :data="mListaSalidasPanelBusqueda"
            row-key="id"
            class="tablePanelControlProduccion"
            header-row-class-name="thead-dark"
            height="calc(100vh - 8.90rem)"
            style="width: 100%"
          >
            <el-table-column
              v-if="isPermisosActions"
              label="Actions"
              width="140"
            >
              <template slot-scope="scope">
                <base-button
                  size="sm"
                  title="EDITAR"
                  type="primary"
                  @click="showEditMercado(scope.row)"
                  ><i class="ni ni-ruler-pencil"></i
                ></base-button>
              </template>
            </el-table-column>

            <!--<el-table-column prop="id_mercado" label="CODIGO" width="130">
            </el-table-column>

            <el-table-column prop="idSali_m" label="Salida" width="140">
              </el-table-column>-->

            <el-table-column
              v-for="column in tableColumnsUnidadesFlotaVehicular"
              :key="column.label"
              v-bind="column"
            >
            </el-table-column>

            <el-table-column label="Estado" min-width="270px" prop="estado">
              <template v-slot="{ row }">
                <badge class="badge-dot mr-4" type="">
                  <!--<i
                      :class="`bg-${
                        row.EstaSali_m == 4
                          ? 'danger'
                          : row.EstaSali_m == 2
                          ? 'warning'
                          : 'success'
                      }`"
                    ></i>-->
                  <span class="status"
                    ><strong>{{
                      row.estado == 1 ? "ACTIVADO" : "ELIMINADO"
                    }}</strong></span
                  >
                </badge>
              </template>
            </el-table-column>

            <div slot="empty"></div>
          </el-table>
        </card>
      </div>
    </base-header>

    <modal :show.sync="modalAddMercado">
      <validation-observer v-slot="{ handleSubmit }" ref="formValidator">
        <form
          class="needs-validation"
          @submit.prevent="handleSubmit(firstFormSubmit)"
        >
          <div class="form-row">
            <div class="col-md-6">
              <base-input
                name="Nombre Procedencia - Sector"
                placeholder="Nombre Procedencia - Sector"
                prepend-icon="ni ni-shop"
                rules="required"
                v-model="mercadoNombre"
              >
              </base-input>
            </div>
            <div class="col-md-6">
              <base-input
                prepend-icon="ni ni-single-02"
                name="Encargado Procedencia - Sector"
                placeholder="Encargado Procedencia - Sector"
                rules="required"
                v-model="encargadoMercado"
              >
              </base-input>
            </div>
          </div>

          <div class="form-row" style="margin-top: 0.5rem">
            <div class="col-md-6">
              <base-input
                name="Email"
                placeholder="Email"
                prepend-icon="ni ni-email-83"
                rules="required"
                v-model="emailMercado"
              >
              </base-input>
            </div>
            <div class="col-md-6">
              <base-input
                prepend-icon="ni ni-mobile-button"
                name="Telefono"
                rules="required"
                placeholder="Telefono"
                v-model="telMercado"
              >
              </base-input>
            </div>
          </div>

          <div class="form-row" style="margin-top: 0.5rem">
            <div class="col-md-12">
              <base-input
                name="Dirección"
                placeholder="Dirección"
                prepend-icon="ni ni-square-pin"
                rules="required"
                v-model="dirMercado"
              >
              </base-input>
            </div>
          </div>

          <div class="text-right" style="margin-top: 1rem">
            <base-button type="danger" @click="closeAddMercado()"
              >Cancelar</base-button
            >
            <base-button
              type="primary"
              @click="registerMercado()"
              native-type="submit"
              >Guardar</base-button
            >
          </div>
        </form>
      </validation-observer>
    </modal>

    <modal :show.sync="modalEditMercado">
      <validation-observer v-slot="{ handleSubmit }" ref="formValidator">
        <form
          class="needs-validation"
          @submit.prevent="handleSubmit(firstFormSubmit)"
        >
          <div class="form-row">
            <div class="col-md-6">
              <base-input
                name="Nombre Procedencia - Sector"
                placeholder="Nombre Procedencia - Sector"
                prepend-icon="ni ni-shop"
                rules="required"
                v-model="mercadoNombre"
              >
              </base-input>
            </div>
            <div class="col-md-6">
              <base-input
                prepend-icon="ni ni-single-02"
                name="Encargado Procedencia - Sector"
                placeholder="Encargado Procedencia - Sector"
                rules="required"
                v-model="encargadoMercado"
              >
              </base-input>
            </div>
          </div>

          <div class="form-row" style="margin-top: 0.5rem">
            <div class="col-md-6">
              <base-input
                name="Email"
                placeholder="Email"
                prepend-icon="ni ni-email-83"
                rules="required"
                v-model="emailMercado"
              >
              </base-input>
            </div>
            <div class="col-md-6">
              <base-input
                prepend-icon="ni ni-mobile-button"
                name="Telefono"
                rules="required"
                type="tel"
                placeholder="Telefono"
                v-model="telMercado"
              >
              </base-input>
            </div>
          </div>

          <div class="form-row" style="margin-top: 0.5rem">
            <div class="col-md-12">
              <base-input
                name="Dirección"
                placeholder="Dirección"
                prepend-icon="ni ni-square-pin"
                rules="required"
                v-model="dirMercado"
              >
              </base-input>
            </div>
          </div>

          <div class="form-row" style="margin-top: 0.5rem">
            <div class="col-md-12">
              <el-select
                style="width: 100%"
                v-model="mSelectEstadoMercado"
                placeholder="ESTADO"
              >
                <el-option label="INACTIVO" :value="0"> </el-option>
                <el-option label="ACTIVO" :value="1"> </el-option>
              </el-select>
            </div>
          </div>

          <div class="text-right" style="margin-top: 1rem">
            <base-button type="danger" @click="closeEditMercado()"
              >Cancelar</base-button
            >
            <base-button
              type="primary"
              @click="updateMercado()"
              native-type="submit"
              >ACTUALIZAR</base-button
            >
          </div>
        </form>
      </validation-observer>
    </modal>
  </div>
</template>
<script>
import jwt_decode from "jwt-decode";
import {
  validarNumeroCelular,
  validarCedula,
  validarCorreoElectronico,
} from "../util/validaciones";
import flatPicker from "vue-flatpickr-component";
import { getBase64LogoReportes } from "../util/logoReport";
import { convertSecondtoTimeString } from "../util/fechas";
import "flatpickr/dist/flatpickr.css";
import { getFecha_dd_mm_yyyy, FechaStringToHour } from "../util/fechas";

import {
  Table,
  TableColumn,
  Select,
  Option,
  Autocomplete,
  DatePicker,
  RadioButton,
  Radio,
  Notification,
  Checkbox,
  CheckboxButton,
  CheckboxGroup,
  Popover,
  Button,
  Loading,
  Switch,
} from "element-ui";

import RouteBreadCrumb from "@/components/argon-core/Breadcrumb/RouteBreadcrumb";
import { BasePagination } from "@/components/argon-core";
import clientPaginationMixin from "~/components/tables/PaginatedTables/clientPaginationMixin";
import swal from "sweetalert2";
import Tabs from "@/components/argon-core/Tabs/Tabs";
import TabPane from "@/components/argon-core/Tabs/Tab";

export default {
  mixins: [clientPaginationMixin],
  layout: "DashboardLayout",
  components: {
    Tabs,
    TabPane,
    BasePagination,
    flatPicker,
    RouteBreadCrumb,
    [Switch.name]: Switch,
    [DatePicker.name]: DatePicker,
    [Select.name]: Select,
    [Option.name]: Option,
    [Table.name]: Table,
    [Notification.name]: Notification,
    [Autocomplete.name]: Autocomplete,
    [TableColumn.name]: TableColumn,
    [RadioButton.name]: RadioButton,
    [Radio.name]: Radio,
    [Checkbox.name]: Checkbox,
    [CheckboxButton.name]: CheckboxButton,
    [CheckboxGroup.name]: CheckboxGroup,
    [Popover.name]: Popover,
    [Button.name]: Button,
  },
  data() {
    return {
      modalAddMercado: false,
      modalEditMercado: false,
      tableColumnsUnidadesFlotaVehicular: [
        {
          prop: "nombre_mercado",
          label: "Procedencia - Sector",
          minWidth: 250,
        },
        
        {
          prop: "cant_organica_mercado",
          label: "C. Organica (KG)",
          minWidth: 170,
        },
        {
          prop: "cant_impropio_mercado",
          label: "C. Impropios (KG)",
          minWidth: 170,
        },
        {
          prop: "encargado_mercado",
          label: "ENCARGADO",
          minWidth: 230,
        },
        {
          prop: "email_mercado",
          label: "EMAIL",
          minWidth: 230,
        },
        {
          prop: "telefono_mercado",
          label: "TELEFONO",
          minWidth: 170,
          sortable: true,
        },
        {
          prop: "dire_mercado",
          label: "DIRECCION",
          minWidth: 240,
        },
      ],
      mListaSalidasPanelBusqueda: [],
      loadingMercado: false,
      mercadoNombre: "",
      encargadoMercado: "",
      emailMercado: "",
      telMercado: "",
      dirMercado: "",
      mSelectEstadoMercado: 1,
      itemSelectMercado: null,
      isPermisosActions: false,
    };
  },
  methods: {
    showEditMercado(item) {
      this.itemSelectMercado = item;
      this.mercadoNombre = item.nombre_mercado;
      this.encargadoMercado = item.encargado_mercado;
      this.emailMercado = item.email_mercado;
      this.telMercado = item.telefono_mercado;
      this.dirMercado = item.dire_mercado;
      this.mSelectEstadoMercado = item.estado;
      this.modalEditMercado = true;
    },
    closeEditMercado() {
      this.modalEditMercado = false;
      this.itemSelectMercado = null;
    },
    showAddMercado() {
      this.clearModalMercado();
      this.modalAddMercado = true;
    },
    closeAddMercado() {
      this.clearModalMercado();
      this.modalAddMercado = false;
    },
    async readMercadosAll() {
      this.loadingMercado = true;
      this.mListaSalidasPanelBusqueda = [];
      try {
        var datos = await this.$axios.get(
          process.env.baseUrl + "/all_mercados",
          {}
        );

        if (datos.data.status_code == 200) {
          /*Notification.success({
              title: "Panel Salidas",
              message: "Datos consultados con éxito",
            });*/
          this.mListaSalidasPanelBusqueda.push(...datos.data.datos);
        } else if (datos.data.status_code == 300) {
          Notification.info({
            title: "Panel Salidas",
            message: "No existen datos disponibles.",
          });
        } else {
          Notification.error({
            title: "Panel Salidas",
            message: datos.data.msm,
          });
        }
      } catch (error) {
        console.log(error);
        Notification.info({
          title: "TryCatch Panel Salidas",
          message: error.toString(),
        });
      }
      this.loadingMercado = false;
    },
    clearModalMercado() {
      this.mercadoNombre = "";
      this.encargadoMercado = "";
      this.emailMercado = "";
      this.telMercado = "";
      this.dirMercado = "";
    },
    async registerMercado() {
      if (
        this.mercadoNombre != "" &&
        this.encargadoMercado != "" &&
        this.emailMercado != "" &&
        this.telMercado != "" &&
        this.dirMercado != ""
      ) {
        try {
          if (validarCorreoElectronico(this.emailMercado)) {
            if (validarNumeroCelular(this.telMercado)) {
              var datos = await this.$axios.post(
                process.env.baseUrlPanel + "/create_mercado",
                {
                  nombre_mercado: this.mercadoNombre,
                  encargado_mercado: this.encargadoMercado,
                  email_mercado: this.emailMercado,
                  telefono_mercado: this.telMercado,
                  dire_mercado: this.dirMercado,
                }
              );
              this.modalAddMercado = false
              this.readMercadosAll();
            } else {
              Notification.error({
                title: "PROCEDENCIA - SECTOR",
                message: "TELEFONO NO VALIDO !",
              });
            }
          } else {
            Notification.error({
              title: "PROCEDENCIA - SECTOR",
              message: "EMAIL NO VALIDO !",
            });
          }
        } catch (error) {
          console.log(error);
        }
      }
    },
    async updateMercado() {
      if (
        this.mercadoNombre != "" &&
        this.encargadoMercado != "" &&
        this.emailMercado != "" &&
        this.telMercado != "" &&
        this.dirMercado != "" &&
        this.itemSelectMercado != null
      ) {
        try {
          if (validarCorreoElectronico(this.emailMercado)) {
            if (validarNumeroCelular(this.telMercado)) {
              var datos = await this.$axios.put(
                process.env.baseUrlPanel + "/update_mercado",
                {
                  id_mercado: this.itemSelectMercado.id_mercado,
                  estado: this.mSelectEstadoMercado,
                  nombre_mercado: this.mercadoNombre,
                  encargado_mercado: this.encargadoMercado,
                  email_mercado: this.emailMercado,
                  telefono_mercado: this.telMercado,
                  dire_mercado: this.dirMercado,
                }
              );
              this.closeEditMercado();
              this.readMercadosAll();
            } else {
              Notification.error({
                title: "PROCEDENCIA - SECTOR",
                message: "TELEFONO NO VALIDO !",
              });
            }
          } else {
            Notification.error({
              title: "PROCEDENCIA - SECTOR",
              message: "EMAIL NO VALIDO !",
            });
          }
        } catch (error) {
          console.log(error);
          alert(error.toString());
        }
      }
    },
  },
  mounted() {
    this.readMercadosAll();
    var data = jwt_decode(this.$cookies.get("token")).datosJWT;
    if (data.active_options_mercado == 1) {
      this.isPermisosActions = true;
    }
  },
};
</script>
<style>
.containerModalRecorridoPanelDespacho {
  display: flex;
}
.cardControlesMarc {
  height: calc(80vh);
  width: 18rem;
}
.current-row {
  background-color: rgba(0, 0, 0, 0.178);
}

.el-table__body tr.current-row > td.el-table__cell {
  background-color: rgba(0, 0, 0, 0.178) !important;
}

.mapa {
  width: 100%;
  height: calc(80vh);
}

.form-controlPersonal {
  display: block;
  width: 100%;
  /* height: calc(1.5em + 1.25rem + 2px); */
  padding: 0.625rem 0.75rem;
  font-size: 0.875rem;
  font-weight: 400;
  line-height: 1.5;
  color: #8898aa;
  background-color: #fff;
  background-clip: padding-box;
  outline: none;
  border: 1px solid #dee2e6;
  border-radius: 0.25rem;
  margin-bottom: 0rem;
  box-shadow: 0 3px 2px rgba(233, 236, 239, 0.05);
  transition: all 0.15s cubic-bezier(0.68, -0.55, 0.265, 1.55);
}

.el-loading-text {
  color: black !important;
}

.el-icon-loading {
  color: black !important;
}

.cardTextoRPagosVehiculoProduccionPanelDespachoBusqueda {
  display: flex;
  align-items: center;
  flex-wrap: wrap;
}

.cardopcinesRPagosVehiculoProduccionPanelDespachoBusqueda {
  display: flex;
  align-items: center;
}

.cardSelectRubrosEstadosPagosVehiculoProduccionContainerPanelDespachoBusqueda {
  display: flex;
  justify-content: space-between;
}

.el-table .warning-row-panelControlProduccion {
  background: rgba(255, 0, 0, 0.342) !important;
}

.el-table .success-row-panelControlProduccion {
  background: hsla(34, 88%, 61%, 0.384) !important;
}

.el-table .finalizado-row-panelControlProduccion {
  background: rgba(140, 248, 126, 0.384) !important;
}

.el-table .diferido-row-panelControlProduccion {
  background: hsla(226, 88%, 61%, 0.274) !important;
}

.no-border-card .card-footer {
  border-top: 0;
}

.card-bodyRPagosVehiculoProduccion {
  padding: 0rem !important;
  height: calc(100vh - 8.59rem);
  overflow: none;
}

.card-bodyTopOpcionesRPagosVehiculoPRoduccionPanelDespachoBusqueda {
  padding-top: 0.25rem !important;
}
.cardOpcinesRPagosVehiculoProduccionPanelDespachoBusqueda {
  display: flex;
  align-items: center;
}
</style>
