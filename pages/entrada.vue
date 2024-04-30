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
            MODULO ENTRADA LOTE
            <el-select
              placeholder="Procedencia - Sector"
              v-model="mSelectMercado"
              multiple
              collapse-tags
              style="margin-right: 0.5rem; margin-left: 1rem"
            >
              <el-option
                v-for="item in mListMercados"
                :key="item.id_mercado"
                :label="item.nombre_mercado"
                :value="item.id_mercado"
              >
              </el-option>
            </el-select>
          </div>

          <div
            class="cardSelectRubrosEstadosPagosVehiculoProduccionContainerPanelDespachoBusqueda"
          >
            <div class="buttonCenterEndDerecha">
              <base-button
                icon
                type="primary"
                size="sm"
                @click="readEntradaAll()"
              >
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
                v-if="banderaActiveTable"
                @click="showAddEntrada()"
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
          body-classes="card-bodyEntrada px-0 pb-1"
          footer-classes="pb-2"
        >
          <el-table
            element-loading-text="Cargando Datos..."
            :data="mListaInsumos"
            row-key="id"
            v-loading="loadingLote"
            class="tablePanelControlProduccion"
            header-row-class-name="thead-dark"
            height="calc(100vh - 10rem)"
            style="width: 100%"
          >
            <el-table-column v-if="banderaActiveTable" width="90">
              <template slot-scope="scope">
                <base-button
                  size="sm"
                  @click="deleteItemEntrada(scope.row.id_entrada)"
                  title="ELIMINAR"
                  type="danger"
                  ><i class="ni ni-fat-remove"></i
                ></base-button>
              </template>
            </el-table-column>

            <!--<el-table-column prop="id_entrada" label="CODIGO" width="130">
            </el-table-column>-->

            <el-table-column
              prop="fecha_hora_entrada_"
              label="FECHA"
              width="190"
            >
            </el-table-column>

            <el-table-column
              prop="nombre_mercado"
              label="PROCEDENCIA"
              width="270"
            >
            </el-table-column>

            <el-table-column
              prop="name_encargado"
              label="ENCARGADO"
              width="200"
            >
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

    <modal :show.sync="modalAddEntrada">
      <validation-observer v-slot="{ handleSubmit }" ref="formValidator">
        <form
          class="needs-validation"
          @submit.prevent="handleSubmit(firstFormSubmit)"
        >
          <div class="form-row">
            <!--<div class="col-md-6">
              <base-input
                name="Codigo Entrada"
                placeholder="Codigo Entrada"
                prepend-icon="ni ni-app"
                rules="required"
                v-model="NombreLote"
              >
              </base-input>
            </div>-->
            <div class="col-md-12">
              <base-input
                prepend-icon="ni ni-single-copy-04"
                name="Nombre Encargado"
                placeholder="Nombre Encargado"
                rules="required"
                v-model="nombre_encargado_entrada"
              >
              </base-input>
            </div>
          </div>

          <div class="form-row" style="margin-bottom: 0.5rem">
            <div class="col-md-6">
              <el-select
                placeholder="Procedencia - Sector"
                v-model="mSelectAddMercado"
                collapse-tags
                style="margin-right: 0.5rem"
              >
                <el-option
                  v-for="item in mListMercados"
                  :key="item.id_mercado"
                  :label="item.nombre_mercado"
                  :value="item.id_mercado"
                >
                </el-option>
              </el-select>
            </div>

            <div class="col-md-6">
              <el-select
                placeholder="Tipo Residuo"
                v-model="mSelectResiduo"
                @change="changeResiduo()"
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
            </div>
          </div>

          <div class="form-row" style="margin-bottom: 0.5rem">
            <div class="col-md-6">
              <base-input
                name="Cant Organico"
                placeholder="Cant Organico"
                v-if="visibleCantOrganico"
                prepend-icon="ni ni-notification-70"
                type="number"
                :min="0"
                v-model="cant_organica_entrada"
              >
              </base-input>
            </div>
            <div class="col-md-6">
              <el-select
                placeholder="Tipo Peso"
                v-if="visibleCantOrganico"
                v-model="mSelectTipoPesoOrganico"
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
            <div class="col-md-6">
              <base-input
                name="Cant Impropios"
                placeholder="Cant Impropios"
                v-if="visibleCantInorganico"
                prepend-icon="ni ni-notification-70"
                type="number"
                :min="0"
                v-model="cant_impropio_entrada"
              >
              </base-input>
            </div>
            <div class="col-md-6">
              <el-select
                placeholder="Tipo Peso"
                v-if="visibleCantInorganico"
                v-model="mSelectTipoPesoImpropio"
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
                name="Observación"
                placeholder="Observación"
                prepend-icon="ni ni-paper-diploma"
                v-model="observacion_entrada"
              >
              </base-input>
            </div>
          </div>

          <div class="text-right">
            <base-button type="danger" @click="clearModalEntrada()"
              >Cancelar</base-button
            >
            <base-button
              type="primary"
              @click="insertNuevaLoteEntrada()"
              native-type="submit"
              >Guardar</base-button
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
      mSelectMercado: null,
      tableColumnsLote: [
        {
          prop: "detalle_residuo",
          label: "T. RESIDUO",
          minWidth: 275,
        },
        {
          prop: "cant_organica",
          label: "CANT ORGANICO (KG)",
          minWidth: 185,
        },
        {
          prop: "cant_impropia",
          label: "CANT IMPROPIOS (KG)",
          minWidth: 195,
        },
        {
          prop: "detalle_entrada",
          label: "DETALLE",
          minWidth: 250,
        },
      ],
      token: this.$cookies.get("token"),
      mListMercados: [],
      loadingLote: false,
      modalAddEntrada: false,
      mListResiduos: [],

      mSelectAddMercado: null,
      mSelectResiduo: null,
      nombre_encargado_entrada: "",
      observacion_entrada: "",
      cant_organica_entrada: null,
      cant_impropio_entrada: null,
      mListTipoPesos: [],
      mSelectTipoPesoImpropio: null,
      mSelectTipoPesoOrganico: null,
      banderaActiveTable: false,
      visibleCantOrganico: false,
      visibleCantInorganico: false,
    };
  },
  methods: {
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
    async readEntradaAll() {
      this.loadingLote = true;
      this.mListaInsumos = [];
      try {
        var datos = await this.$axios.post(
          process.env.baseUrl + "/read_entradas_all",
          {
            token: this.token,
            mercado:
              this.mSelectMercado != null && this.mSelectMercado.length > 0
                ? this.mSelectMercado
                : "*",
          }
        );

        if (datos.data.status_code == 200) {
          /*Notification.success({
              title: "Panel Salidas",
              message: "Datos consultados con éxito",
            });*/
          this.mListaInsumos.push(...datos.data.datos);
        } else if (datos.data.status_code == 300) {
          Notification.info({
            title: "Entradas",
            message: "No existen datos disponibles.",
          });
        } else {
          Notification.error({
            title: "Entradas",
            message: datos.data.msm,
          });
        }
      } catch (error) {
        console.log(error);
        Notification.info({
          title: "TryCatch Entradas",
          message: error.toString(),
        });
      }
      this.loadingLote = false;
    },
    async readTipoMercadoActivo() {
      this.mListMercados = [];
      try {
        var datos = await this.$axios.get(
          process.env.baseUrl + "/all_mercados_active"
        );
        console.log(datos.data.datos);
        this.mListMercados.push(...datos.data.datos);
      } catch (error) {
        console.log(error);
      }
    },
    showAddEntrada() {
      this.modalAddEntrada = true;
    },
    async insertNuevaLoteEntrada() {
      try {
        if (this.nombre_encargado_entrada != "") {
          if (this.mSelectAddMercado != null) {
            if (this.mSelectResiduo != null) {
              if (
                this.mSelectResiduo == 1 &&
                (this.cant_organica_entrada == null ||
                  this.mSelectTipoPesoOrganico == null)
              ) {
                Notification.info({
                  title: "ENTRADA",
                  message: "SELECCIONE UN TIPO DE PESO / INGRESE UNA CANTIDAD",
                });

                return;
              } else if (
                this.mSelectResiduo == 2 &&
                (this.cant_impropio_entrada == null ||
                  this.mSelectTipoPesoImpropio == null)
              ) {
                Notification.info({
                  title: "ENTRADA",
                  message: "SELECCIONE UN TIPO DE PESO / INGRESE UNA CANTIDAD",
                });

                return;
              } else if (
                this.mSelectResiduo == 3 &&
                this.cant_impropio_entrada == null &&
                this.cant_organica_entrada == null &&
                (this.mSelectTipoPesoImpropio == null ||
                  this.mSelectTipoPesoOrganico == null)
              ) {
                Notification.info({
                  title: "ENTRADA",
                  message: "SELECCIONE UN TIPO DE PESO / INGRESE UNA CANTIDAD",
                });

                return;
              } else {
                var data = {
                  token: this.token,
                  fk_id_mercado: this.mSelectAddMercado,
                  cant_organica:
                    this.cant_organica_entrada == null
                      ? 0
                      : this.convertPesoOrganico(this.cant_organica_entrada),
                  cant_impropia:
                    this.cant_impropio_entrada == null
                      ? 0
                      : this.convertPesoImpropio(this.cant_impropio_entrada),
                  name_encargado: this.nombre_encargado_entrada,
                  fk_tipo_residuo: this.mSelectResiduo,
                  detalle_entrada: this.observacion_entrada,
                };

                if ((data.cant_impropia == 0 && data.fk_tipo_residuo == 2)) {
                  Notification.info({
                    title: "ENTRADA",
                    message: "INGRESE UNA CANTIDAD DE IMPROPIO VALIDA",
                  });
                } else if (
                  (data.cant_organica == 0 && data.fk_tipo_residuo == 1)
                ) {
                  Notification.info({
                    title: "ENTRADA",
                    message: "INGRESE UNA CANTIDAD ORGANICA VALIDA",
                  });
                } else {
                  if (
                    data.cant_organica == 0 &&
                    data.cant_impropia == 0 &&
                    data.fk_tipo_residuo == 3
                  ) {
                    Notification.info({
                      title: "ENTRADA",
                      message: "INGRESE CANTIDADES ORGANICAS E IMPROPIAS VALIDAS",
                    });
                  } else {

                    /**ULTIMAS VALIDAcIONES**/
                    console.log(data);

                    var datos = await this.$axios.post(
                      process.env.baseUrlPanel + "/create_entrada",
                      data
                    );
                    if (datos.data.status_code == 200) {
                      this.clearModalEntrada();
                      this.readEntradaAll();
                    } else {
                      Notification.info({
                        title: "ENTRADA",
                        message: datos.data.msm,
                      });
                    }
                  }
                }
              }
            } else {
              Notification.info({
                title: "ENTRADA",
                message: "SELECCIONE UN RESIDUO VALIDO",
              });
            }
          } else {
            Notification.info({
              title: "ENTRADA",
              message: "SELECCIONE UNA PROCEDENCIA VALIDO",
            });
          }
        } else {
          Notification.info({
            title: "ENTRADA",
            message: "NOMBRE DEL ENCARGADO NO VALIDO",
          });
        }
      } catch (error) {
        console.log(error);
      }
    },
    clearModalEntrada() {
      this.modalAddEntrada = false;
      this.mSelectAddMercado = null;
      this.mSelectResiduo = null;
      this.nombre_encargado_entrada = "";
      this.observacion_entrada = "";
      this.cant_organica_entrada = null;
      this.cant_impropio_entrada = null;
    },
    async deleteItemEntrada(id_entrada) {
      console.log(id_entrada);
      try {
        var datos = await this.$axios.delete(
          process.env.baseUrl + "/delete_entrada",
          {
            data: {
              token: this.token,
              id_entrada: id_entrada,
            },
          }
        );

        if (datos.data.status_code == 200) {
          this.readEntradaAll();
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
    convertPesoOrganico(peso) {
      if (this.mSelectTipoPesoOrganico != null) {
        if (this.mSelectTipoPesoOrganico == 1) {
          peso = toneladasAKilogramos(peso);
        } else if (this.mSelectTipoPesoOrganico == 2) {
          peso = librasAKilogramos(peso);
        }
      }

      return peso;
    },
    convertPesoImpropio(peso) {
      if (this.mSelectTipoPesoImpropio != null) {
        if (this.mSelectTipoPesoImpropio == 1) {
          peso = toneladasAKilogramos(peso);
        } else if (this.mSelectTipoPesoImpropio == 2) {
          peso = librasAKilogramos(peso);
        }
      }

      return peso;
    },
    changeResiduo() {
      this.visibleCantOrganico = false;
      this.visibleCantInorganico = false;
      if (this.mSelectResiduo != null) {
        if (this.mSelectResiduo == 1) {
          this.visibleCantOrganico = true;
        } else if (this.mSelectResiduo == 2) {
          this.visibleCantInorganico = true;
        } else {
          this.visibleCantInorganico = true;
          this.visibleCantOrganico = true;
        }
      }
    },
  },
  mounted() {
    this.readTipoPesosActivo();
    this.readResiduosAll();
    this.readTipoMercadoActivo();
    this.readEntradaAll();

    var data = jwt_decode(this.$cookies.get("token")).datosJWT;
    if (data.active_options_Entrada == 1) {
      this.banderaActiveTable = true;
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

.card-bodyEntrada {
  padding: 0rem !important;
  height: calc(100vh - 9.5rem);
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
