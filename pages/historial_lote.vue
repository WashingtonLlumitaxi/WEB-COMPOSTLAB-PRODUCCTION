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
          >
          MODULO HISTORIAL PILA
        </div>

          <div
            class="cardSelectRubrosEstadosPagosVehiculoProduccionContainerPanelDespachoBusqueda"
          >
            <div class="buttonCenterEndDerecha">
              <base-button
                icon
                type="primary"
                size="sm"
                @click="readHistorialLoteAll()"
              >
                <span class="btn-inner--icon"
                  ><i class="el-icon-search"></i
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
            :data="mListaHistorialLote"
            v-loading="loadingHLote"
            row-key="id"
            class="tablePanelControlProduccion"
            header-row-class-name="thead-dark"
            height="calc(100vh - 8.90rem)"
            style="width: 100%"
          >
            <el-table-column v-if="isPermisosActions" width="170">
              <template slot-scope="scope">
                <base-button
                  size="sm"
                  title="DESPACHO"
                  type="danger"
                  @click="showNotificationDespacho(scope.row)"
                  ><i class="ni ni-check-bold"></i
                ></base-button>
                <base-button
                  size="sm"
                  title="AGREGAR OBSERVACION"
                  type="success"
                  @click="showAddHistorial(scope.row)"
                  ><i class="ni ni-fat-add"></i
                ></base-button>
                <base-button
                  size="sm"
                  title="AGREGAR INSUMO"
                  type="default"
                  @click="showAddInsumoLote(scope.row)"
                  ><i class="ni ni-cart"></i
                ></base-button>
              </template>
            </el-table-column>

            <!--<el-table-column prop="id_lote" label="CODIGO" width="130">
            </el-table-column>-->

            <el-table-column prop="nombre_lote" label="PILA" width="180">
            </el-table-column>

            <el-table-column prop="detalle_actividad" label="ACTIVIDAD" width="190">
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

            <!--<el-table-column prop="idSali_m" label="Salida" width="140">
              </el-table-column>-->
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

    <modal :show.sync="modalAddInsumo" size="xl">
      <h6 slot="header" class="modal-title">
        {{
          itemRowHistorialLote == null ? "" : itemRowHistorialLote.nombre_lote
        }}
      </h6>

      <div class="form-row" style="margin-bottom: 0.5rem">
        <div class="col-md-3">
          <el-select
            placeholder="Tipo Insumo"
            v-model="mSelectInsumo"
            style="width: 100%"
          >
            <el-option
              v-for="item in mListInsumoALLActivo"
              :key="item.id_insumo"
              :label="item.nombre_insumo"
              :value="item.id_insumo"
            >
            </el-option>
          </el-select>
        </div>

        <div class="col-md-2">
          <base-input
            name="Cantidad"
            placeholder="Cantidad"
            v-model="cantInsumo"
            :min="0"
            type="number"
          >
          </base-input>
        </div>
        <div class="col-md-3">
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
        <div class="col-md-3">
          <base-button
            size="lg"
            title="HISTORIAL"
            @click="addInsumoLote()"
            type="primary"
            >INGRESO INSUMO</base-button
          >
        </div>
      </div>

      <el-table
        element-loading-text="Cargando Datos..."
        :data="mListInsumoXLote"
        row-key="id"
        class="tablePanelControlProduccion"
        v-loading="loadingHLoteInsumo"
        header-row-class-name="thead-dark"
        height="calc(100vh - 20rem)"
        style="width: 100%"
      >
        <el-table-column v-if="isPermisosActions" width="100">
          <template slot-scope="scope">
            <base-button
              size="sm"
              title="ELIMINAR"
              type="danger"
              @click="deleteItemInsumoLote(scope.row)"
              ><i class="ni ni-fat-remove"></i
            ></base-button>
          </template>
        </el-table-column>

        <el-table-column prop="fk_id_lote" label="CODIGO" width="120">
        </el-table-column>

        <el-table-column
          v-for="column in tableInsumoLotes"
          :key="column.label"
          v-bind="column"
        >
        </el-table-column>

        <div slot="empty"></div>
      </el-table>
    </modal>

    <modal :show.sync="modalAddHistorial" size="xl">
      <h6 slot="header" class="modal-title">
        CUADRO DE CONDICIONES FISICO/QUIMICOS DEL PROCESO COMPOST
        {{
          itemRowHistorialLote == null ? "" : itemRowHistorialLote.nombre_lote
        }}
      </h6>

      <div class="form-row" style="margin-bottom: 0.5rem">
        <div class="col-md-2">
          <base-input
            name="TEMPERATURA"
            placeholder="TEMPERATURA"
            type="number"
            rules="required"
            :min="0"
            :max="70"
            v-model="vTemperatura"
          >
          </base-input>
        </div>
        <div class="col-md-2">
          <base-input
            v-model="vHumedad"
            name="HUMEDAD"
            rules="required"
            placeholder="HUMEDAD"
            type="number"
            :min="0"
            :max="100"
          >
          </base-input>
        </div>
        <div class="col-md-2">
          <base-input
            v-model="vPh"
            name="PH"
            placeholder="PH"
            rules="required"
            :min="1"
            :max="14"
            type="number"
          >
          </base-input>
        </div>

        <div class="col-md-2">
          <el-select
            placeholder="Tipo Actividad"
            v-model="mSelectTipoActividad"
            style="width: 100%"
          >
            <el-option
              v-for="item in mListActividad"
              :key="item.id_actividad"
              :label="item.detalle_actividad"
              :value="item.id_actividad"
            >
            </el-option>
          </el-select>
        </div>

        <!--<div class="col-md-2">
          <base-input
            v-model="vOxigeno"
            name="OXIGENO"
            placeholder="OXIGENO"
            rules="required"
            type="number"
          >
          </base-input>
        </div>-->
        <div class="col-md-2">
          <base-input
            v-model="detalleHistorial"
            name="DETALLE"
            placeholder="DETALLE"
          >
          </base-input>
        </div>

        <div class="col-md-2">
          <base-button
            icon
            type="primary"
            @click="insertDetalleHsitorialLote()"
          >
            <span class="btn-inner--icon"
              ><i class="ni ni-fat-add"></i>AGREGAR</span
            >
          </base-button>
        </div>
      </div>

      <el-table
        element-loading-text="Cargando Datos..."
        :data="tableDetalleHistorialLote"
        row-key="id"
        class="tablePanelControlProduccion"
        v-loading="loadingtableDetalleHistorialLote"
        header-row-class-name="thead-dark"
        height="calc(100vh - 20rem)"
        style="width: 100%"
      >
        <el-table-column v-if="isPermisosActions" width="100">
          <template slot-scope="scope">
            <base-button
              size="sm"
              @click="deleteItemHistorialLote(scope.row.id_historial_lote)"
              title="ELIMINAR"
              type="danger"
              ><i class="ni ni-fat-remove"></i
            ></base-button>
          </template>
        </el-table-column>

        <el-table-column
          v-for="column in tableHistorialDetalleLote"
          :key="column.label"
          v-bind="column"
        >
        </el-table-column>

        <el-table-column prop="detalle_actividad" label="ACTIVIDAD" width="170">
          <template slot-scope="scope">
            <badge
              v-if="scope.row.id_actividad == null"
              type="secondary"
              class="mr-2"
              >Sin Actividad</badge
            >

            <badge
              v-if="scope.row.id_actividad == 1"
              type="warning"
              class="mr-2"
              >{{ scope.row.detalle_actividad }}</badge
            >

            <badge
              v-if="scope.row.id_actividad == 2"
              type="default"
              class="mr-2"
              >{{ scope.row.detalle_actividad }}</badge
            >

            <badge
              v-if="scope.row.id_actividad == 3"
              type="primary"
              class="mr-2"
              >{{ scope.row.detalle_actividad }}</badge
            >

            <badge
              v-if="scope.row.id_actividad == 4"
              type="info"
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

        <el-table-column
          prop="detalleHistorial"
          label="DETALLE RESIDUO"
          width="180"
        >
        </el-table-column>

        <div slot="empty"></div>
      </el-table>
    </modal>
  </div>
</template>
<script>
import flatPicker from "vue-flatpickr-component";
import RouteBreadCrumb from "~/components/argon-core/Breadcrumb/RouteBreadcrumb";
import BaseHeader from "~/components/argon-core/BaseHeader";

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
  MessageBox,
} from "element-ui";
import jwt_decode from "jwt-decode";

import { BasePagination } from "@/components/argon-core";
import clientPaginationMixin from "~/components/tables/PaginatedTables/clientPaginationMixin";

import Tabs from "@/components/argon-core/Tabs/Tabs";
import TabPane from "@/components/argon-core/Tabs/Tab";

export default {
  mixins: [clientPaginationMixin],
  layout: "DashboardLayout",
  components: {
    BaseHeader,
    RouteBreadCrumb,
    Tabs,
    TabPane,
    BasePagination,
    flatPicker,
    [MessageBox.name]: MessageBox,
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
      mSelectTipoPeso: null,
      mListTipoPesos: [],
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
          minWidth: 180,
        },
        {
          prop: "nombre_mercado",
          label: "Procedencia - Sector",
          minWidth: 220,
        },
      ],
      modalAddInsumo: false,
      mListaHistorialLote: [],
      mListaInsumoLote: [],
      itemRowHistorialLote: null,
      token: this.$cookies.get("token"),
      cantInsumo: 0,
      mListInsumoALLActivo: [],
      mSelectInsumo: null,
      tableInsumoLotes: [
        {
          prop: "nombre_lote",
          label: "PILA",
          minWidth: 210,
        },
        {
          prop: "nombre_insumo",
          label: "INSUMO",
          minWidth: 210,
        },
        {
          prop: "fecha_ingreso",
          label: "FECHA",
          minWidth: 170,
        },
        {
          prop: "cantidad",
          label: "CANT",
          minWidth: 100,
        },
        {
          prop: "detalle_tipo_peso",
          label: "TIPO PESO",
          minWidth: 100,
        },
      ],
      tableHistorialDetalleLote: [
        {
          prop: "fechaHistorial",
          label: "FECHA",
          minWidth: 150,
        },
        {
          prop: "vTemperatura",
          label: "TEMP °C",
          minWidth: 100,
        },
        {
          prop: "vHumedad",
          label: "HUMEDAD %",
          minWidth: 130,
        },
        {
          prop: "vPh",
          label: "PH",
          minWidth: 100,
        }
      ],
      mListInsumoXLote: [],
      loadingHLote: false,
      loadingHLoteInsumo: false,
      modalAddHistorial: false,
      loadingtableDetalleHistorialLote: false,
      tableDetalleHistorialLote: [],
      vTemperatura: null,
      vHumedad: null,
      vPh: null,
      vOxigeno: null,
      detalleHistorial: "",
      isPermisosActions: false,
      mSelectTipoActividad: null,
      mListActividad: [],
    };
  },
  methods: {
    showAddHistorial(item) {
      this.itemRowHistorialLote = item;
      this.modalAddHistorial = true;
      this.readDetalleHistorialLote();
    },
    showAddInsumoLote(item) {
      this.itemRowHistorialLote = item;
      this.readInsumoLote(item.id_lote);
      this.modalAddInsumo = true;
    },
    clearModalDetalleHistorial() {
      this.vTemperatura = null;
      this.vHumedad = null;
      this.vPh = null;
      //this.vOxigeno = null;
      this.detalleHistorial = "";
    },
    async readInsumoAll() {
      this.mListInsumoALLActivo = [];
      try {
        var datos = await this.$axios.get(
          process.env.baseUrl + "/all_insumos",
          {}
        );
        this.mListInsumoALLActivo.push(...datos.data.datos);
      } catch (error) {
        console.log(error);
      }
    },
    async readHistorialLoteAll() {
      this.loadingHLote = true;
      this.mListaHistorialLote = [];
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
          this.mListaHistorialLote.push(...datos.data.datos);
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
      this.loadingHLote = false;
    },
    async readInsumoLote(lote) {
      this.mListInsumoXLote = [];
      this.loadingHLoteInsumo = true;
      try {
        var datos = await this.$axios.get(
          process.env.baseUrl + "/all_insumo_lote/" + lote
        );
        this.mListInsumoXLote.push(...datos.data.datos);
      } catch (error) {
        console.log(error);
      }
      this.loadingHLoteInsumo = false;
    },
    async addInsumoLote() {
      if (this.mSelectTipoPeso != null) {
        if (this.mSelectInsumo != null) {
          this.mListInsumoXLote = [];
          try {
            var datos = await this.$axios.post(
              process.env.baseUrl + "/add_insumo_lote",
              {
                id_lote: this.itemRowHistorialLote.id_lote,
                id_insumo: this.mSelectInsumo,
                cantidad: this.cantInsumo,
                fk_id_peso: this.mSelectTipoPeso,
              }
            );
            if (datos.data.status_code == 200) {
              this.readInsumoLote(this.itemRowHistorialLote.id_lote);
            } else {
              Notification.info({
                title: "ADD INSUMO",
                message: datos.data.msm,
              });
            }
          } catch (error) {
            console.log(error);
          }
        } else {
          this.showNotificationError("AGREGAR INSUMO", "SELECIONAR UN INSUMO");
        }
      } else {
        this.showNotificationError(
          "AGREGAR INSUMO",
          "SELECIONAR UN TIPO DE PESO"
        );
      }
    },
    showNotificationError(title, msm) {
      Notification.error({
        title: title,
        message: msm,
      });
    },
    async readDetalleHistorialLote() {
      this.loadingtableDetalleHistorialLote = true;
      this.tableDetalleHistorialLote = [];

      try {
        var datos = await this.$axios.get(
          process.env.baseUrl +
            "/readDetalleLote/" +
            this.itemRowHistorialLote.id_lote
        );
        if (datos.data.status_code == 200 || datos.data.status_code == 300) {
          this.tableDetalleHistorialLote.push(...datos.data.datos);
        } else {
          this.showNotificationError("HISTORIAL PILA", datos.data.msm);
        }
      } catch (error) {
        this.showNotificationError("TRY CATCH", error.toString());
      }

      this.loadingtableDetalleHistorialLote = false;
    },
    async insertDetalleHsitorialLote() {
      if (
        this.vTemperatura != null &&
        this.vHumedad != null &&
        this.vPh != null &&
        this.mSelectTipoActividad != null
        //&&
        //this.vOxigeno != null
      ) {
        if (this.vTemperatura >= 0 && this.vTemperatura <= 70) {
          if (this.vHumedad >= 0 && this.vHumedad <= 100) {
            if (this.vPh >= 1 && this.vPh <= 14) {
              if (this.mSelectTipoActividad != null) {
                try {
                  var datos = await this.$axios.post(
                    process.env.baseUrl + "/add_historial_lote",
                    {
                      token: this.token,
                      vTemperatura: this.vTemperatura,
                      vHumedad: this.vHumedad,
                      vPh: this.vPh,
                      vOxigeno: 0,
                      detalleHistorial: this.detalleHistorial,
                      lote: this.itemRowHistorialLote.id_lote,
                      actividad: this.mSelectTipoActividad,
                    }
                  );
                  if (datos.data.status_code == 200) {
                    this.clearModalDetalleHistorial();
                    this.readDetalleHistorialLote();
                    this.readHistorialLoteAll();
                  } else {
                    this.showNotificationError(
                      "HISTORIAL PILA",
                      datos.data.msm
                    );
                  }
                } catch (error) {
                  this.showNotificationError("TRY CATCH", error.toString());
                }
              } else {
                this.showNotificationError(
                  "HISTORIAL PILA",
                  "SELECCIONE UNA ACTIVIDAD"
                );
              }
            } else {
              this.showNotificationError(
                "HISTORIAL PILA",
                "PH PERMITIDO ENTRE 1 a 14"
              );
            }
          } else {
            this.showNotificationError(
              "HISTORIAL PILA",
              "HUMEDAD PERMITIDA ENTRE 0% a 100%"
            );
          }
        } else {
          this.showNotificationError(
            "HISTORIAL PILA",
            "TEMPERATURA PERMITIDA ENTRE 0 a 70 grados centígrados"
          );
        }
      } else {
        this.showNotificationError("HISTORIAL PILA", "DATOS VACIOS");
      }
    },
    showNotificationDespacho(item) {
      MessageBox.confirm(
        "Desea enviar el " + item.nombre_lote + " a despacho ?",
        "DESPACHO",
        {
          confirmButtonText: "DESPACHAR",
          cancelButtonText: "Cancel",
          type: "warning",
        }
      )
        .then(() => {
          this.sendDespacho(item.id_lote);
        })
        .catch((e) => {
          this.showNotificationError(item.nombre_lote, e.toString());
        });
    },
    async sendDespacho(lote) {
      try {
        var datos = await this.$axios.put(
          process.env.baseUrl + "/sendLoteDespacho",
          {
            token: this.token,
            lote: lote,
          }
        );
        if (datos.data.status_code == 200) {
          this.readHistorialLoteAll();
        } else {
          this.showNotificationError("DESPACHO", datos.data.msm);
        }
      } catch (error) {
        this.showNotificationError("TRY CATCH DESPACHO", error.toString());
      }
    },
    async deleteItemHistorialLote(id_historial_lote) {
      console.log(id_historial_lote);
      try {
        var datos = await this.$axios.delete(
          process.env.baseUrl + "/deleteItemHistorialLote",
          {
            data: {
              token: this.token,
              itemHLote: id_historial_lote,
            },
          }
        );

        if (datos.data.status_code == 200) {
          this.readHistorialLoteAll();
          this.readDetalleHistorialLote();
        } else {
          alert(datos.data.msm);
        }
      } catch (error) {
        alert(error.toString());
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
    async deleteItemInsumoLote(obj) {
      console.log(obj);
      try {
        var datos = await this.$axios.delete(
          process.env.baseUrl + "/delete_insumo_lote",
          {
            data: {
              token: this.token,
              id_insumo_lote: obj.id_insumo_lote,
            },
          }
        );

        if (datos.data.status_code == 200) {
          this.readHistorialLoteAll();
          this.readInsumoLote(obj.fk_id_lote);
        } else {
          alert(datos.data.msm);
        }
      } catch (error) {
        alert(error.toString());
      }
    },
    async readTipoActividad() {
      this.mListActividad = [];
      try {
        var datos = await this.$axios.get(
          process.env.baseUrl + "/read_actividad"
        );
        this.mListActividad.push(...datos.data.datos);
      } catch (error) {
        console.log(error);
      }
    },
  },
  mounted() {
    this.readTipoActividad();
    this.readTipoPesosActivo();
    this.readInsumoAll();
    this.readHistorialLoteAll();

    var data = jwt_decode(this.$cookies.get("token")).datosJWT;
    if (data.active_options_historial_lote == 1) {
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
