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
          MODULO SALIDAS
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
            <el-table-column v-if="isPermisosActions" label="" width="180">
              <template slot-scope="scope">
                <base-button
                  size="sm"
                  title="ENVIAR A SALIDA"
                  type="danger"
                  v-if="scope.row.fechaDespacho == null"
                  @click="showNotificationDespacho(scope.row)"
                  ><i class="ni ni-check-bold"></i>AUT. SALIDA</base-button
                >
                <base-button
                  size="sm"
                  title="ENTREGADO"
                  type="default"
                  v-if="scope.row.fechaDespacho != null"
                  @click="showEditEntregado(scope.row)"
                  >ENTREGADO</base-button
                >

              </template>
            </el-table-column>

            <!--<el-table-column prop="id_lote" label="CODIGO" width="130">
            </el-table-column>

            <el-table-column prop="idSali_m" label="Salida" width="140">
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

        <modal :show.sync="modalDespachoAuth" modal-classes="modal-sm">
          <validation-observer v-slot="{ handleSubmit }" ref="formValidator">
            <form
              class="needs-validation"
              @submit.prevent="handleSubmit(firstFormSubmit)"
            >
              <h6 slot="header" class="modal-title">
                Desea enviar el
                {{
                  itemSelectDespacho == null
                    ? ""
                    : itemSelectDespacho.nombre_lote
                }}
                a salida ?
              </h6>

              <div class="form-row" style="margin-top: 1rem">
                <div class="col-md-12">
                  <base-input
                    name="DESTINO"
                    placeholder="DESTINO"
                    prepend-icon="ni ni-single-02"
                    rules="required"
                    v-model="destinoSalida"
                  >
                  </base-input>
                </div>
              </div>
              <div class="form-row">
                <div class="col-md-12">
                  <base-input
                    name="CORREO DESTINO"
                    placeholder="CORREO"
                    prepend-icon="ni ni-email-83"
                    rules="required"
                    v-model="correoSalida"
                  >
                  </base-input>
                </div>
              </div>
              <div class="form-row">
                <div class="col-md-12">
                  <base-input
                    name="TELEFONO"
                    placeholder="TELEFONO"
                    type="tel"
                    prepend-icon="ni ni-mobile-button"
                    rules="required"
                    v-model="telefonoSalida"
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
              <div class="text-right">
                <base-button type="danger" @click="closeModalDespacho()"
                  >Cancelar</base-button
                >
                <base-button
                  type="primary"
                  @click="sendAuthLoteSalida()"
                  native-type="submit"
                  >Autorizar</base-button
                >
              </div>
            </form>
          </validation-observer>
        </modal>

        <modal :show.sync="modalDespachoEdit" modal-classes="modal-sm">
          <validation-observer v-slot="{ handleSubmit }" ref="formValidator">
            <form
              class="needs-validation"
              @submit.prevent="handleSubmit(firstFormSubmit)"
            >
              <h6 slot="header" class="modal-title">
                {{nameLote}}
              </h6>

              <div class="form-row" style="margin-top: 1rem">
                <div class="col-md-12">
                  <base-input
                    name="DESTINO"
                    placeholder="DESTINO"
                    prepend-icon="ni ni-single-02"
                    rules="required"
                    v-model="destinoSalida"
                  >
                  </base-input>
                </div>
              </div>
              <div class="form-row">
                <div class="col-md-12">
                  <base-input
                    name="CORREO DESTINO"
                    placeholder="CORREO"
                    prepend-icon="ni ni-email-83"
                    rules="required"
                    v-model="correoSalida"
                  >
                  </base-input>
                </div>
              </div>
              <div class="form-row">
                <div class="col-md-12">
                  <base-input
                    name="TELEFONO"
                    placeholder="TELEFONO"
                    type="tel"
                    prepend-icon="ni ni-mobile-button"
                    rules="required"
                    v-model="telefonoSalida"
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
              <div class="text-right">
                <base-button type="danger" @click="closeModalLoteSalida()"
                  >Cancelar</base-button
                >
                <base-button
                  type="primary"
                  @click="updateDatoSalida()"
                  native-type="submit"
                  >Actualizar</base-button
                >
              </div>
            </form>
          </validation-observer>
        </modal>

      </div>
    </base-header>
  </div>
</template>
<script>
import flatPicker from "vue-flatpickr-component";
import {
  validarNumeroCelular,
  validarCedula,
  validarCorreoElectronico,
} from "../util/validaciones";
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
  MessageBox,
} from "element-ui";
import jwt_decode from "jwt-decode";
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
      tableColumnsLote: [
        {
          prop: "nombre_lote",
          label: "PILA",
          minWidth: 190,
        },
        {
          prop: "vOxigeno",
          label: "OXIGENO",
          minWidth: 150,
        },
        {
          prop: "vPh",
          label: "PH",
          minWidth: 120,
        },
        {
          prop: "vHumedad",
          label: "HUMEDAD",
          minWidth: 150,
        },
        {
          prop: "vTemperatura",
          label: "TEMPERATURA",
          minWidth: 150,
        },
        {
          prop: "fechaIngreso",
          label: "F. INGRESO",
          minWidth: 230,
        },
        {
          prop: "fechaDespacho",
          label: "F. DESPACHO",
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
      mListaHistorialLote: [],
      itemRowHistorialLote: null,
      token: this.$cookies.get("token"),
      isPermisosActions: false,
      loadingHLote: false,
      itemSelectDespacho: null,
      modalDespachoAuth: false,
      destinoSalida: null,
      correoSalida: null,
      telefonoSalida: null,
      nameLote:"",
      modalDespachoEdit:false
    };
  },
  methods: {
    async readHistorialLoteAll() {
      this.loadingHLote = true;
      this.mListaHistorialLote = [];
      try {
        var datos = await this.$axios.post(
          process.env.baseUrl + "/despacho_lote_usuer",
          { token: this.token }
        );

        if (datos.data.status_code == 200 || datos.data.status_code == 300) {
          /*Notification.success({
              title: "Panel Salidas",
              message: "Datos consultados con éxito",
            });*/
          this.mListaHistorialLote.push(...datos.data.datos);
        } else {
          Notification.error({
            title: "PILAS DESPACHO",
            message: datos.data.msm,
          });
        }
      } catch (error) {
        console.log(error);
        Notification.info({
          title: "TryCatch PILAS DESPACHO",
          message: error.toString(),
        });
      }
      this.loadingHLote = false;
    },
    showNotificationError(title, msm) {
      Notification.error({
        title: title,
        message: msm,
      });
    },
    showNotificationDespacho(item) {
      this.modalDespachoAuth = true;
      this.itemSelectDespacho = item;

      /*MessageBox.confirm(
        "Desea enviar el " + item.nombre_lote + " a salida ?",
        "SALIDA",
        {
          confirmButtonText: "CONFIRMAR",
          cancelButtonText: "Cancel",
          type: "warning",
        }
      )
        .then(() => {
          this.sendAuthLoteSalida(item);
        })
        .catch((e) => {
          this.showNotificationError(item.nombre_lote, e.toString());
        });*/
    },
    async sendAuthLoteSalida() {
      try {
        if (
          this.destinoSalida != null &&
          this.correoSalida != null &&
          this.telefonoSalida != null
        ) {
          if (this.destinoSalida != null) {
            if (this.telefonoSalida != null) {
              if (this.correoSalida != null) {
                if (validarCorreoElectronico(this.correoSalida)) {
                  if (validarNumeroCelular(this.telefonoSalida)) {
                    var datos = await this.$axios.post(
                      process.env.baseUrl + "/authLoteSalida",
                      {
                        token: this.token,
                        lote: this.itemSelectDespacho.id_lote,
                        destinoSalida: this.destinoSalida,
                        correoSalida: this.correoSalida,
                        telefonoSalida: this.telefonoSalida,
                      }
                    );

                    if (datos.data.status_code == 200) {
                      /*Notification.success({
              title: "Panel Salidas",
              message: "Datos consultados con éxito",
            });*/
                      this.closeModalDespacho();
                      this.readHistorialLoteAll();
                    } else {
                      Notification.error({
                        title: "PILAS DESPACHO",
                        message: datos.data.msm,
                      });
                    }
                  } else {
                    this.showNotificationError(
                      "AUT. SALIDA",
                      "TELEFONO NO VALIDA.!!"
                    );
                  }
                } else {
                  this.showNotificationError(
                    "AUT. SALIDA",
                    "CORREO ELECTRONICO NO VALIDA.!!"
                  );
                }
              } else {
                this.showNotificationError(
                  "AUT. SALIDA",
                  "INGRESE UN CORREO VALIDO.!"
                );
              }
            } else {
              this.showNotificationError(
                "AUT. SALIDA",
                "INGRESE UN TELEFONO VALIDO"
              );
            }
          } else {
            this.showNotificationError(
              "AUT. SALIDA",
              "INGRESE UN DESTINO VALIDO.!"
            );
          }
        } else {
          this.showNotificationError("AUT. SALIDA", "EXISTEN DATOS VACIOS");
        }
      } catch (error) {
        console.log(error);
        Notification.info({
          title: "TryCatch PILAS DESPACHO",
          message: error.toString(),
        });
      }
    },
    closeModalDespacho() {
      this.itemSelectDespacho = null;
      this.modalDespachoAuth = false;
      this.destinoSalida = null;
      this.correoSalida = null;
      this.telefonoSalida = null;
    },
    closeModalLoteSalida() {
      this.itemSelectDespacho = null;
      this.modalDespachoEdit = false;
      this.destinoSalida = null;
      this.correoSalida = null;
      this.telefonoSalida = null;
    },
    showEditEntregado(item)
    {
      console.log(item)
      this.itemSelectDespacho = item
      this.modalDespachoEdit = true
      this.nameLote = item.nombre_lote
      this.destinoSalida = item.destino
      this.correoSalida = item.correo_destino
      this.telefonoSalida = item.telefono_destino
    },

    async updateDatoSalida() {
      try {
        if (
          this.destinoSalida != null &&
          this.correoSalida != null &&
          this.telefonoSalida != null
        ) {
          if (this.destinoSalida != null) {
            if (this.telefonoSalida != null) {
              if (this.correoSalida != null) {
                if (validarCorreoElectronico(this.correoSalida)) {
                  if (validarNumeroCelular(this.telefonoSalida)) {
                    var datos = await this.$axios.put(
                      process.env.baseUrl + "/update_lote_salida",
                      {
                        token: this.token,
                        id_lote: this.itemSelectDespacho.id_lote,
                        destino: this.destinoSalida,
                        email: this.correoSalida,
                        telefono: this.telefonoSalida,
                      }
                    );

                    if (datos.data.status_code == 200) {
                      /*Notification.success({
              title: "Panel Salidas",
              message: "Datos consultados con éxito",
            });*/
                      this.closeModalLoteSalida();
                      this.readHistorialLoteAll();
                    } else {
                      Notification.error({
                        title: "DATOS SALIDA",
                        message: datos.data.msm,
                      });
                    }
                  } else {
                    this.showNotificationError(
                      "DATOS. SALIDA",
                      "TELEFONO NO VALIDA.!!"
                    );
                  }
                } else {
                  this.showNotificationError(
                    "DATOS. SALIDA",
                    "CORREO ELECTRONICO NO VALIDA.!!"
                  );
                }
              } else {
                this.showNotificationError(
                  "DATOS. SALIDA",
                  "INGRESE UN CORREO VALIDO.!"
                );
              }
            } else {
              this.showNotificationError(
                "DATOS. SALIDA",
                "INGRESE UN TELEFONO VALIDO"
              );
            }
          } else {
            this.showNotificationError(
              "DATOS. SALIDA",
              "INGRESE UN DESTINO VALIDO.!"
            );
          }
        } else {
          this.showNotificationError("DATOS. SALIDA", "EXISTEN DATOS VACIOS");
        }
      } catch (error) {
        console.log(error);
        Notification.info({
          title: "TryCatch PILAS DESPACHO",
          message: error.toString(),
        });
      }
    },
  },
  mounted() {
    var data = jwt_decode(this.$cookies.get("token")).datosJWT;
    if (data.active_options_despacho == 1) {
      this.isPermisosActions = true;
    }

    this.readHistorialLoteAll();
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
