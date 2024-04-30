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
          >MODULO INSUMOS</div>

          <div
            class="cardSelectRubrosEstadosPagosVehiculoProduccionContainerPanelDespachoBusqueda"
          >
            <div class="buttonCenterEndDerecha">
              <base-button
                icon
                type="primary"
                size="sm"
                @click="readInsumoAll()"
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
                @click="showAddInsumo()"
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
            :data="mListaSalidasPanelBusqueda"
            v-loading="loadingInsumo"
            row-key="id"
            class="tablePanelControlProduccion"
            header-row-class-name="thead-dark"
            height="calc(100vh - 8.90rem)"
            style="width: 100%"
          >
            <el-table-column  v-if="isPermisosActions" label="" width="100">
              <template slot-scope="scope">
                <base-button
                  size="sm"
                  title="EDITAR"
                  type="primary"
                  @click="showEditInsumo(scope.row)"
                  ><i class="ni ni-ruler-pencil"></i
                ></base-button>
              </template>
            </el-table-column>

            <!--<el-table-column prop="id_insumo" label="CODIGO" width="130">
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
                      row.activo == 1 ? "ACTIVADO" : "ELIMINADO"
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
          <div class="form-row" style="margin-bottom: 0.5rem">
            <div class="col-md-12">
              <base-input
                name="Nombre Insumo"
                placeholder="Nombre Insumo"
                prepend-icon="ni ni-cart"
                rules="required"
                v-model="nombre_insumo"
              >
              </base-input>
            </div>
            <!--<div class="col-md-6">
              <base-input
                prepend-icon="ni ni-tag"
                name="Origin Insumo"
                placeholder="Origin Insumo"
                rules="required"
                v-model="origin_insumo"
              >
              </base-input>
            </div>-->
          </div>
          <div class="form-row" style="margin-bottom: 0.5rem">
            <div class="col-md-6">
              <base-input
                prepend-icon="ni ni-single-copy-04"
                name="Cantidad"
                placeholder="Cantidad"
                v-model="cantidad_insumo"
                rules="required"
                type="number"
              >
              </base-input>
            </div>
            <div class="col-md-6">
              <el-select
                placeholder="Tipo Peso"
                v-model="mSelectTipoPeso"
                style="width: 100%"
              >
                <el-option
                v-for="item in mListTipoPesos"
                  :key="item.id_tipo_peso"
                  :label="item.detalle_tipo_peso"
                  :value="item.id_tipo_peso"
                >
                </el-option>
              </el-select>
            </div>
          </div>
          <div class="form-row" style="margin-bottom: 0.5rem">
            <div class="col-md-12">
              <el-select
                placeholder="Tipo Insumo"
                v-model="mSelectInsumo"
                style="width: 100%"
              >
                <el-option
                  v-for="item in mListaInsumos"
                  :key="item.id_tipo_insumo"
                  :label="item.nombreTipoInsumo"
                  :value="item.id_tipo_insumo"
                >
                </el-option>
              </el-select>
            </div>
          </div>
          <div class="form-row" style="margin-bottom: 1rem">
            <div class="col-md-6">
              <base-input
                name="Precio Insumo"
                placeholder="Precio Insumo"
                prepend-icon="ni ni-money-coins"
                rules="required"
                v-model="precio_insumo"
                type="number"
              >
              </base-input>
            </div>
            <div class="col-md-6">
              <base-input
                prepend-icon="ni ni-book-bookmark"
                name="Detalle Insumo"
                placeholder="Detalle Insumo"
                v-model="decrip_insumo"
                rules="required"
              >
              </base-input>
            </div>
          </div>
          <div class="text-right">
            <base-button type="danger" @click="clearModalInsumo()"
              >Cancelar</base-button
            >
            <base-button
              type="primary"
              @click="insertInsumo()"
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
          <div class="form-row" style="margin-bottom: 0.5rem">
            <div class="col-md-12">
              <base-input
                name="Nombre Insumo"
                placeholder="Nombre Insumo"
                prepend-icon="ni ni-cart"
                rules="required"
                v-model="nombre_insumo"
              >
              </base-input>
            </div>
            <!--<div class="col-md-6">
              <base-input
                prepend-icon="ni ni-tag"
                name="Origin Insumo"
                placeholder="Origin Insumo"
                rules="required"
                v-model="origin_insumo"
              >
              </base-input>
            </div>-->
          </div>
          <div class="form-row" style="margin-bottom: 0.5rem">
            <div class="col-md-6">
              <base-input
                prepend-icon="ni ni-single-copy-04"
                name="Cantidad"
                placeholder="Cantidad"
                v-model="cantidad_insumo"
                rules="required"
                type="number"
              >
              </base-input>
            </div>
            <div class="col-md-6">
              <el-select
                placeholder="Tipo Peso"
                v-model="mSelectTipoPeso"
                style="width: 100%"
              >
              <el-option
                v-for="item in mListTipoPesos"
                  :key="item.id_tipo_peso"
                  :label="item.detalle_tipo_peso"
                  :value="item.id_tipo_peso"
                >
                </el-option>
              </el-select>
            </div>
          </div>
          <div class="form-row" style="margin-bottom: 0.5rem">
            <div class="col-md-12">
              <el-select
                placeholder="Tipo Insumo"
                v-model="mSelectInsumo"
                style="width: 100%"
              >
                <el-option
                  v-for="item in mListaInsumos"
                  :key="item.id_tipo_insumo"
                  :label="item.nombreTipoInsumo"
                  :value="item.id_tipo_insumo"
                >
                </el-option>
              </el-select>
            </div>
          </div>
          <div class="form-row" style="margin-bottom: 1rem">
            <div class="col-md-6">
              <base-input
                name="Precio Insumo"
                placeholder="Precio Insumo"
                prepend-icon="ni ni-money-coins"
                rules="required"
                v-model="precio_insumo"
                type="number"
              >
              </base-input>
            </div>
            <div class="col-md-6">
              <base-input
                prepend-icon="ni ni-book-bookmark"
                name="Detalle Insumo"
                placeholder="Detalle Insumo"
                v-model="decrip_insumo"
                rules="required"
              >
              </base-input>
            </div>
          </div>
          <div class="text-right">
            <base-button type="danger" @click="closeEditInsumo()"
              >Cancelar</base-button
            >
            <base-button type="primary" @click="updateInsumo()" native-type="submit"
              >Actualizar</base-button
            >
          </div>
        </form>
      </validation-observer>
    </modal>
  </div>
</template>
<script>
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
import jwt_decode from "jwt-decode";
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
          prop: "nombre_insumo",
          label: "INSUMO",
          minWidth: 230,
        },
        {
          prop: "nombreTipoInsumo",
          label: "TIPO INSUMO",
          minWidth: 250,
        },
        {
          prop: "cantidad_insumo",
          label: "CANT",
          minWidth: 120,
        },
        {
          prop: "detalle_tipo_peso",
          label: "T. PESO",
          minWidth: 180,
        },
        {
          prop: "precio_insumo",
          label: "PRECIO",
          minWidth: 140,
        },
        {
          prop: "decrip_insumo",
          label: "DETALLE",
          minWidth: 400,
        },
      ],
      mListaSalidasPanelBusqueda: [],
      mListaInsumos: [],
      mSelectInsumo: null,

      nombre_insumo: "",
      origin_insumo: "",
      cantidad_insumo: "",
      precio_insumo: "",
      decrip_insumo: "",

      loadingInsumo: false,
      mSelectInsumoList: null,
      mListTipoPesos:[],
      mSelectTipoPeso:null,
      isPermisosActions:false
    };
  },
  methods: {
    showEditInsumo(item) {
      this.mSelectInsumoList = item
      this.mSelectInsumo = item.fk_id_tipo_insumo;
      this.nombre_insumo = item.nombre_insumo;
      this.origin_insumo = item.origin_insumo;
      this.cantidad_insumo = item.cantidad_insumo;
      this.precio_insumo = item.precio_insumo;
      this.decrip_insumo = item.decrip_insumo;
      this.modalEditMercado = true;
      this.mSelectTipoPeso = item.id_tipo_peso
    },
    closeEditInsumo() {
      this.modalEditMercado = false;
      this.mSelectInsumoList = null;
    },
    showAddInsumo() {
      this.clearModalInsumo();
      this.modalAddMercado = true;
    },
    async readInsumoAll() {
      this.loadingInsumo = true;
      this.mListaSalidasPanelBusqueda = [];
      try {
        var datos = await this.$axios.get(
          process.env.baseUrl + "/all_insumos",
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

      this.loadingInsumo = false;
    },
    async readTipoInsumoActive() {
      this.mListaInsumos = [];
      try {
        var datos = await this.$axios.get(
          process.env.baseUrl + "/all_tipo_insumos"
        );
        this.mListaInsumos.push(...datos.data.datos);
      } catch (error) {
        console.log(error);
      }
    },
    clearModalInsumo() {
      this.modalAddMercado = false
      this.nombre_insumo = ""
      this.origin_insumo = ""
      this.cantidad_insumo = ""
      this.precio_insumo = ""
      this.decrip_insumo = ""

      this.mSelectInsumo = null;
    },
    async insertInsumo() {
      if (
        this.nombre_insumo != "" &&
        this.cantidad_insumo != "" &&
        this.precio_insumo != "" &&
        this.decrip_insumo != "" &&
        this.mSelectInsumo != null &&
        this.mSelectTipoPeso != null
      ) {
        try {
          var datos = await this.$axios.post(
            process.env.baseUrlPanel + "/create_insumo",
            {
              nombre_insumo: this.nombre_insumo,
              origin_insumo: "",
              id_tipo_insumo: this.mSelectInsumo,
              cantidad_insumo: this.cantidad_insumo,
              precio_insumo: this.precio_insumo,
              decrip_insumo: this.decrip_insumo,
              tipo_peso: this.mSelectTipoPeso == null ? 1 : this.mSelectTipoPeso
            }
          );
          if(datos.data.status_code == 200){
            this.clearModalInsumo()
          }
          this.readInsumoAll();
        } catch (error) {
          console.log(error);
        }
      }
    },
    async updateInsumo() {
      if (
        this.nombre_insumo != "" &&
        this.cantidad_insumo != "" &&
        this.precio_insumo != "" &&
        this.decrip_insumo != "" &&
        this.mSelectInsumo != null &&
        this.mSelectInsumoList != null &&
        this.mSelectTipoPeso != null
      ) {
        try {
          var datos = await this.$axios.put(
            process.env.baseUrlPanel + "/update_insumo",
            {
              id_insumo:this.mSelectInsumoList.id_insumo,
              nombre_insumo: this.nombre_insumo,
              origin_insumo: "",
              id_tipo_insumo: this.mSelectInsumo,
              cantidad_insumo: this.cantidad_insumo,
              precio_insumo: this.precio_insumo,
              decrip_insumo: this.decrip_insumo,
              tipo_peso: this.mSelectTipoPeso
            }
          );
          this.closeEditInsumo()
          this.readInsumoAll();
        } catch (error) {
          console.log(error);
          alert(error.toString())
        }
      }
    },
    async readTipoPesosActivo() {
      this.mListTipoPesos = [];
      try {
        var datos = await this.$axios.get(
          process.env.baseUrl + "/tipo_peso_active"
        );
        console.log(datos.data.datos);
        this.mListTipoPesos.push(...datos.data.datos);
      } catch (error) {
        console.log(error);
      }
    },
  },
  mounted() {
    this.readTipoPesosActivo()
    this.readTipoInsumoActive();
    this.readInsumoAll();

    var data = jwt_decode(this.$cookies.get("token")).datosJWT;
    if(data.active_options_insumo == 1){
      this.isPermisosActions = true
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
