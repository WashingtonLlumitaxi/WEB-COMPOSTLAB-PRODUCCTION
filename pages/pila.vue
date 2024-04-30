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
          <div class="cardTextoRPagosVehiculoProduccionPanelDespachoBusqueda">
            MODULO PILA
          </div>

          <div
            class="cardSelectRubrosEstadosPagosVehiculoProduccionContainerPanelDespachoBusqueda"
          >
            <div class="buttonCenterEndDerecha">
              <base-button icon type="primary" size="sm" @click="readLoteAll()">
                <span class="btn-inner--icon"
                  ><i class="el-icon-search"></i
                ></span>
              </base-button>
              <!--<download-excel
              class="btn btn-outline-success"
              outline
              :header="headerExcelRPagosVehiculoProduccion"
              :data="mListaInsumos"
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
                @click="showAddLote()"
                title="NUEVO PILA"
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
            :data="mListaInsumos"
            row-key="id"
            v-loading="loadingLote"
            class="tablePanelControlProduccion"
            header-row-class-name="thead-dark"
            height="calc(100vh - 8.90rem)"
            style="width: 100%"
          >
            <el-table-column v-if="isPermisosActions" width="120">
              <template slot-scope="scope">
                <base-button
                  size="sm"
                  @click="showEditLote(scope.row)"
                  title="EDITAR"
                  type="primary"
                  ><i class="ni ni-ruler-pencil"></i
                ></base-button>
                <base-button
                  size="sm"
                  @click="deleteNuevoLote(scope.row.id_lote)"
                  title="ELIMINAR"
                  type="danger"
                  ><i class="ni ni-fat-remove"></i
                ></base-button>
              </template>
            </el-table-column>

            <!--<el-table-column prop="id_lote" label="CODIGO" width="130">
            </el-table-column>-->

            <el-table-column prop="nombre_lote" label="PILA" width="190">
            </el-table-column>

            <!--<el-table-column
              prop="detalle_residuo"
              label="TIPO RESIDUO"
              width="270"
            >
            </el-table-column>-->

            <el-table-column
              prop="detalle_actividad"
              label="ACTIVIDAD"
              width="190"
            >
              <template slot-scope="scope">
                <badge
                  v-if="scope.row.id_actividad == null"
                  type="primary"
                  class="mr-2"
                  >Sin Actividad</badge
                >

                <badge
                  v-if="scope.row.id_actividad == 1"
                  type="success"
                  class="mr-2"
                  >{{ scope.row.detalle_actividad }}</badge
                >

                <badge
                  v-if="scope.row.id_actividad == 2"
                  type="success"
                  class="mr-2"
                  >{{ scope.row.detalle_actividad }}</badge
                >

                <badge
                  v-if="scope.row.id_actividad == 3"
                  type="success"
                  class="mr-2"
                  >{{ scope.row.detalle_actividad }}</badge
                >

                <badge
                  v-if="scope.row.id_actividad == 4"
                  type="success"
                  class="mr-2"
                  >{{ scope.row.detalle_actividad }}</badge
                >

                <badge
                  v-if="scope.row.id_actividad == 5"
                  type="success"
                  class="mr-2"
                  >{{ scope.row.detalle_actividad }}</badge
                >

                <badge
                  v-if="scope.row.id_actividad == 6"
                  type="danger"
                  class="mr-2"
                  >{{ scope.row.detalle_actividad }}</badge
                >
              </template>
            </el-table-column>

            <el-table-column prop="detalleFase" label="FASE ACT." width="190">
              <template slot-scope="scope">
                <badge
                  v-if="scope.row.FkIDFase == 4"
                  type="success"
                  class="mr-2"
                  >{{ scope.row.detalleFase }}</badge
                >

                <badge
                  v-if="scope.row.FkIDFase == 1"
                  type="primary"
                  class="mr-2"
                  >{{ scope.row.detalleFase }}</badge
                >

                <badge
                  v-if="scope.row.FkIDFase == 2"
                  type="info"
                  class="mr-2"
                  >{{ scope.row.detalleFase }}</badge
                >

                <badge
                  v-if="scope.row.FkIDFase == 3"
                  type="default"
                  class="mr-2"
                  >{{ scope.row.detalleFase }}</badge
                >

                <badge
                  v-if="scope.row.FkIDFase == 5"
                  type="danger"
                  class="mr-2"
                  >{{ scope.row.detalleFase }}</badge
                >
              </template>
            </el-table-column>

            <!--<el-table-column prop="idSali_m" label="Salida" width="140">
              </el-table-column>-->

            <el-table-column
              v-for="column in tableColumnsLote"
              :key="column.label"
              v-bind="column"
            >
            </el-table-column>

            <div slot="empty"></div>
          </el-table>
        </card>
      </div>
    </base-header>

    <modal :show.sync="modalAddLote">
      <validation-observer v-slot="{ handleSubmit }" ref="formValidator">
        <form
          class="needs-validation"
          @submit.prevent="handleSubmit(firstFormSubmit)"
        >
          <div class="form-row" style="margin-bottom: 0.5rem">
            <div class="col-md-12">
              <base-input
                name="Nombre Pila"
                placeholder="Nombre Pila"
                prepend-icon="ni ni-app"
                rules="required"
                v-model="NombreLote"
              >
              </base-input>
            </div>

            <!--<div class="col-md-6">
              <el-select
                placeholder="Tipo Residuo"
                v-model="mSelectResiduo"
                style="width: 100%"
              >
                <el-option
                  v-for="item in mListResiduos"
                  :key="item.id_residuo"
                  :label="item.detalle_residuo"
                  :value="item.id_residuo"
                >
                </el-option>
              </el-select>
            </div>-->
          </div>

          <div class="form-row" style="margin-bottom: 0.5rem">
            <div class="col-md-6">
              <el-select
                placeholder="Procedencia - Sector"
                v-model="mSelectMercado"
                @change="cambioSelectProdencia()"
                style="width: 100%"
              >
                <el-option
                  v-for="item in mListTipoMercados"
                  :key="item.id_mercado"
                  :label="item.nombre_mercado"
                  :value="item.id_mercado"
                >
                </el-option>
              </el-select>
            </div>
            <div class="col-md-6">
              <base-input
                name="Peso Orgánico"
                placeholder="Peso Orgánico"
                prepend-icon="ni ni-box-2"
                append-icon="KG"
                type="number"
                disabled
                v-model="PesoActualMercado"
              >
              </base-input>
            </div>
          </div>

          <div class="form-row" style="margin-bottom: 0.5rem">
            <div class="col-md-6">
              <base-input
                prepend-icon="ni ni-single-copy-04"
                name="Ingrese Peso"
                placeholder="Ingrese Peso"
                v-model="PesoLote"
                rules="required"
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
              <base-input
                name="Dias Notificación"
                placeholder="Dias Notificación"
                prepend-icon="ni ni-notification-70"
                type="number"
                v-model="DiaNotificationLote"
              >
              </base-input>
            </div>
          </div>

          <div class="form-row" style="margin-bottom: 0.5rem">
            <div class="col-md-12">
              <base-input
                name="Detalle Pila"
                placeholder="Detalle Pila"
                prepend-icon="ni ni-paper-diploma"
                rules="required"
                v-model="ObsLote"
              >
              </base-input>
            </div>
          </div>

          <div class="text-right">
            <base-button type="danger" @click="clearModalAddLote()"
              >Cancelar</base-button
            >
            <base-button
              type="primary"
              @click="insertNuevoLote()"
              native-type="submit"
              >Guardar</base-button
            >
          </div>
        </form>
      </validation-observer>
    </modal>

    <modal :show.sync="modalEditLote">
      <validation-observer v-slot="{ handleSubmit }" ref="formValidator">
        <form
          class="needs-validation"
          @submit.prevent="handleSubmit(firstFormSubmit)"
        >
          <div class="form-row" style="margin-bottom: 0.5rem">
            <div class="col-md-12">
              <base-input
                name="Nombre Pila"
                placeholder="Nombre Pila"
                prepend-icon="ni ni-app"
                rules="required"
                v-model="NombreLote"
              >
              </base-input>
            </div>
            <!--<div class="col-md-6">
              <el-select
                placeholder="Tipo Residuo"
                v-model="mSelectResiduo"
                style="width: 100%"
              >
                <el-option
                  v-for="item in mListResiduos"
                  :key="item.id_residuo"
                  :label="item.detalle_residuo"
                  :value="item.id_residuo"
                >
                </el-option>
              </el-select>
            </div>-->
          </div>

          <div class="form-row" style="margin-bottom: 0.5rem">
            <div class="col-md-6">
              <el-select
                placeholder="Procedencia - Sector"
                v-model="mSelectMercado"
                disabled
                style="width: 100%"
              >
                <el-option
                  v-for="item in mListTipoMercados"
                  :key="item.id_mercado"
                  :label="item.nombre_mercado"
                  :value="item.id_mercado"
                >
                </el-option>
              </el-select>
            </div>

            <div class="col-md-6">
              <base-input
                name="Peso Orgánico"
                placeholder="Peso Orgánico"
                prepend-icon="ni ni-box-2"
                append-icon="KG"
                type="number"
                disabled
                v-model="PesoActualMercado"
              >
              </base-input>
            </div>
          </div>

          <div class="form-row" style="margin-bottom: 0.5rem">
            <div class="col-md-6">
              <base-input
                prepend-icon="ni ni-single-copy-04"
                name="Ingrese Peso"
                placeholder="Ingrese Peso"
                v-model="PesoLote"
                rules="required"
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
                placeholder="Tipo Fase"
                v-model="mSelectTipoFase"
                style="width: 100%"
              >
                <el-option
                  v-for="item in mListFases"
                  :key="item.idFase"
                  :label="item.detalleFase"
                  :value="item.idFase"
                >
                </el-option>
              </el-select>
            </div>
          </div>

          <div class="form-row" style="margin-bottom: 0.5rem">
            <div class="col-md-12">
              <base-input
                name="Detalle Pila"
                placeholder="Detalle Pila"
                prepend-icon="ni ni-paper-diploma"
                rules="required"
                v-model="ObsLote"
              >
              </base-input>
            </div>
          </div>

          <div class="form-row" style="margin-bottom: 0.5rem">
            <!--<div class="col-md-6">
              <el-select
                placeholder="Estado"
                v-model="mSelectEstadoLote"
                style="width: 100%"
              >
                <el-option label="ACTIVO" :value="1"> </el-option>
                <el-option label="INACTIVO" :value="0"> </el-option>
              </el-select>
            </div>-->

            <div class="col-md-12">
              <base-input
                name="Dias Notificación"
                placeholder="Dias Notificación"
                prepend-icon="ni ni-notification-70"
                type="number"
                v-model="DiaNotificationLote"
              >
              </base-input>
            </div>
          </div>

          <div class="text-right">
            <base-button type="danger" @click="closeEditLote()"
              >Cancelar</base-button
            >
            <base-button
              type="primary"
              @click="updateNuevoLote()"
              native-type="submit"
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
import "flatpickr/dist/flatpickr.css";
import {
  getFecha_dd_mm_yyyy,
  FechaStringToHour,
  convertSecondtoTimeString,
} from "../util/fechas";
import { librasAKilogramos, toneladasAKilogramos } from "../util/convert";

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
      mListResiduos: [],
      mSelectResiduo: null,
      modalAddLote: false,
      modalEditLote: false,
      mSelectMercado: null,
      tableColumnsLote: [
        {
          prop: "fechaIngreso",
          label: "F. INGRESO",
          minWidth: 230,
        },
        {
          prop: "peso",
          label: "PESO",
          minWidth: 120,
        },
        {
          prop: "detalle_tipo_peso",
          label: "TIPO PESO",
          minWidth: 140,
        },
        {
          prop: "observacion_lote",
          label: "DETALLE",
          minWidth: 250,
        },
        {
          prop: "nombre_mercado",
          label: "Procedencia - Sector",
          minWidth: 250,
        },
        {
          prop: "UsuarioNombres",
          label: "USUARIO",
          minWidth: 330,
        },
      ],
      mSelectTipoPeso: null,
      mListaInsumos: [],
      token: this.$cookies.get("token"),
      mListTipoPesos: [],
      mListTipoMercados: [],
      loadingLote: false,
      DiaNotificationLote: null,
      NombreLote: null,
      ObsLote: null,
      PesoLote: null,
      mSelectEstadoLote: 0,
      itemSelectLote: null,
      isPermisosActions: false,
      mListFases: [],
      mSelectTipoFase: null,
      PesoActualMercado: 0.0,
    };
  },
  methods: {
    showEditLote(item) {
      this.readTipoMercadoActivo()
      this.itemSelectLote = item;
      this.mSelectEstadoLote = item.activo;
      this.mSelectTipoPeso = item.id_tipo_peso;
      this.mSelectMercado = item.id_mercado;
      this.DiaNotificationLote = item.dia_notificacion;
      this.NombreLote = item.nombre_lote;
      this.ObsLote = item.observacion_lote;
      this.PesoLote = item.peso;
      this.mSelectResiduo = item.id_residuo;
      this.mSelectTipoFase = item.FkIDFase;
      this.modalEditLote = true;
      this.cambioSelectProdencia()
    },
    closeEditLote() {
      this.modalEditLote = false;
    },
    showAddLote() {
      this.clearModalAddLote();
      this.modalAddLote = true;
    },
    async readLoteAll() {
      this.loadingLote = true;
      this.mListaInsumos = [];
      try {
        var datos = await this.$axios.post(
          process.env.baseUrl + "/lote_usuer",
          { token: this.token }
        );

        if (datos.data.status_code == 200) {
          /*Notification.success({
              title: "Panel Salidas",
              message: "Datos consultados con éxito",
            });*/
          this.mListaInsumos.push(...datos.data.datos);
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
      this.loadingLote = false;
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
    async readResiduosAll() {
      this.mListResiduos = [];
      try {
        var datos = await this.$axios.get(
          process.env.baseUrl + "/read_residuos_all"
        );
        console.log(datos.data.datos);
        this.mListResiduos.push(...datos.data.datos);
      } catch (error) {
        console.log(error);
      }
    },
    async readTipoMercadoActivo() {
      this.mListTipoMercados = [];
      try {
        var datos = await this.$axios.get(
          process.env.baseUrl + "/all_mercados_active"
        );
        console.log(datos.data.datos);
        this.mListTipoMercados.push(...datos.data.datos);
      } catch (error) {
        console.log(error);
      }
    },
    async insertNuevoLote() {
      try {
        if (
          this.NombreLote != null &&
          this.ObsLote != null &&
          this.PesoLote != null &&
          this.mSelectMercado != null &&
          this.mSelectTipoPeso != null
        ) {
          if (this.checkPeso()) {
            var datos = await this.$axios.post(
              process.env.baseUrlPanel + "/add_lote_usuer",
              {
                token: this.token,
                nombre_lote: this.NombreLote,
                observacion_lote: this.ObsLote,
                peso: this.PesoLote,
                tipo_peso: this.mSelectTipoPeso,
                id_mercado: this.mSelectMercado,
                dia_notification:
                  this.DiaNotificationLote == null
                    ? 0
                    : this.DiaNotificationLote,
                residuo: 1 /*this.mSelectResiduo,*/,
              }
            );

            if (datos.data.status_code == 200) {
              this.readTipoMercadoActivo();
              this.clearModalAddLote();
              this.readLoteAll();
            } else {
              Notification.info({
                title: "AGREGAR PILA",
                message: datos.data.msm,
              });
            }
          } else {
            Notification.info({
              title: "AGREGAR PILA",
              message: "EL PESO NO DEBE SER MAYO AL PERMITIDO",
            });
          }
        } else {
          Notification.info({
            title: "AGREGAR PILA",
            message: "EXISTEN DATOS VACIOS",
          });
        }
      } catch (error) {
        console.log(error);
      }
    },
    clearModalAddLote() {
      this.modalAddLote = false;
      this.NombreLote = null;
      this.ObsLote = null;
      this.PesoLote = null;
      this.mSelectMercado = null;
      this.mSelectTipoPeso = null;
      this.mSelectResiduo = null;
    },
    async updateNuevoLote() {
      try {
        if (
          this.NombreLote != null &&
          this.ObsLote != null &&
          this.PesoLote != null &&
          this.mSelectMercado != null &&
          this.mSelectTipoPeso != null &&
          this.mSelectTipoPeso != null
        ) {
          if (this.checkPeso()) {
            var datos = await this.$axios.put(
              process.env.baseUrlPanel + "/update_lote",
              {
                token: this.token,
                id_lote: this.itemSelectLote.id_lote,
                estado: this.mSelectEstadoLote,
                nombre_lote: this.NombreLote,
                observacion_lote: this.ObsLote,
                peso: this.PesoLote,
                tipo_peso: this.mSelectTipoPeso,
                id_mercado: this.mSelectMercado,
                dia_notification:
                  this.DiaNotificationLote == null
                    ? 0
                    : this.DiaNotificationLote,
                residuo: 1,
                fase: this.mSelectTipoFase,
              }
            );

            if (datos.data.status_code == 200) {
              this.closeEditLote();
              this.readLoteAll();
            } else {
              Notification.info({
                title: "ACTUALIZACION PILA",
                message: datos.data.msm,
              });
            }
          } else {
            Notification.info({
              title: "AGREGAR PILA",
              message: "EL PESO NO DEBE SER MAYO AL PERMITIDO",
            });
          }
        }
      } catch (error) {
        console.log(error);
      }
    },
    async deleteNuevoLote(id_lote) {
      try {
        var datos = await this.$axios.delete(
          process.env.baseUrlPanel + "/eliminar_lote",
          {
            data: {
              token: this.token,
              id_lote: id_lote,
            },
          }
        );

        if (datos.data.status_code == 200) {
          this.readLoteAll();
        } else {
          Notification.info({
            title: "ELIMINACION DE PILA",
            message: datos.data.msm,
          });
        }
      } catch (error) {
        console.log(error);
      }
    },
    async readFases() {
      this.mListFases = [];
      try {
        var datos = await this.$axios.get(process.env.baseUrl + "/read_fase");
        console.log(datos.data.datos);
        this.mListFases.push(...datos.data.datos);
      } catch (error) {
        console.log(error);
      }
    },
    cambioSelectProdencia() {
      if (this.mSelectMercado != null) {
        for (var i = 0; i < this.mListTipoMercados.length > 0; i++) {
          if (this.mListTipoMercados[i].id_mercado == this.mSelectMercado) {
            this.PesoActualMercado =
              this.mListTipoMercados[i].cant_organica_mercado;
          }
        }
      }
    },
    checkPeso() {
      console.log("mSelectTipoPeso : "+this.mSelectTipoPeso)
      console.log("PesoLote : "+this.PesoLote)
      if (this.mSelectTipoPeso != null) {
        if (this.mSelectTipoPeso == 1) {
          console.log(toneladasAKilogramos(this.PesoLote))
          if (this.PesoActualMercado >= toneladasAKilogramos(this.PesoLote)) 
          {
            return true;
          }
        } else if (this.mSelectTipoPeso == 2) {
          if (this.PesoActualMercado >= librasAKilogramos(this.PesoLote)) {
            return true;
          }
        } else {
          if (this.PesoActualMercado >= this.PesoLote) {
            return true;
          }
        }
      }
      return false;
    },
  },
  mounted() {
    this.readFases();
    this.readResiduosAll();
    this.readTipoMercadoActivo();
    this.readTipoPesosActivo();
    this.readLoteAll();

    var data = jwt_decode(this.$cookies.get("token")).datosJWT;
    if (data.active_options_lote == 1) {
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
