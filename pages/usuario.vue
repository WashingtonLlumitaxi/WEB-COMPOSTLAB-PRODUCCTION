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
          MODULO USUARIOS
        </div>

          <div
            class="cardSelectRubrosEstadosPagosVehiculoProduccionContainerPanelDespachoBusqueda"
          >
            <div class="buttonCenterEndDerecha">
              <base-button
                icon
                type="primary"
                size="sm"
                @click="readUsuarioALL()"
              >
                <span class="btn-inner--icon"
                  ><i class="el-icon-search"></i
                ></span>
              </base-button>
              <base-button
                type="default"
                size="sm"
                @click="showModalAddUsuario()"
                title="NUEVO USUARIO"
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
            :data="mListUsuarios"
            row-key="id"
            v-loading="loadingUsuarios"
            class="tablePanelControlProduccion"
            header-row-class-name="thead-dark"
            height="calc(100vh - 8.90rem)"
            style="width: 100%"
          >
            <el-table-column label="Actions" width="180">
              <template slot-scope="scope">
                <base-button
                  size="sm"
                  title="EDITAR"
                  @click="showModalUpdateInfoUsuario(scope.row)"
                  type="primary"
                  ><i class="ni ni-ruler-pencil"></i
                ></base-button>
                <base-button
                  size="sm"
                  @click="showModalUpdatePassword(scope.row)"
                  title="CONTRASEÑA"
                  type="default"
                  ><i class="ni ni-key-25"></i
                ></base-button>
                <base-button
                  size="sm"
                  title="PERMISOS"
                  @click="showModalPermisosUsuario(scope.row)"
                  type="danger"
                  ><i class="ni ni-like-2"></i
                ></base-button>
              </template>
            </el-table-column>

            <el-table-column
              v-for="column in tableColumnsUsuarios"
              :key="column.label"
              v-bind="column"
            >
            </el-table-column>

            <el-table-column prop="estado" label="ESTADO" width="150">
              <template slot-scope="scope">
                {{ scope.row.estado == 1 ? "ACTIVADO" : "INACTIVO" }}
              </template>
            </el-table-column>

            <div slot="empty"></div>
          </el-table>
        </card>
      </div>

      <modal :show.sync="modalAddUsuario">
        <validation-observer v-slot="{ handleSubmit }" ref="formValidator">
          <form
            class="needs-validation"
            @submit.prevent="handleSubmit(firstFormSubmit)"
          >
            <div class="form-row">
              <div class="col-md-6">
                <base-input
                  name="Nombres Usuario"
                  placeholder="Nombres Usuario"
                  prepend-icon="ni ni-single-02"
                  rules="required"
                  v-model="nombres"
                >
                </base-input>
              </div>
              <div class="col-md-6">
                <base-input
                  prepend-icon="ni ni-single-02"
                  name="Apellidos Usuario"
                  placeholder="Apellidos Usuario"
                  rules="required"
                  v-model="apellido"
                >
                </base-input>
              </div>
            </div>

            <div class="form-row" style="margin-top: 0.5rem">
              <div class="col-md-6">
                <base-input
                  name="Cedula / DNI"
                  placeholder="Cedula / DNI"
                  prepend-icon="ni ni-badge"
                  type="number"
                  rules="required"
                  v-model="cedula"
                >
                </base-input>
              </div>
              <div class="col-md-6">
                <base-input
                  prepend-icon="ni ni-mobile-button"
                  name="Telefono Usuario"
                  type="tel"
                  rules="required"
                  placeholder="Telefono Usuario"
                  v-model="telefono"
                >
                </base-input>
              </div>
            </div>

            <div class="form-row" style="margin-top: 0.5rem">
              <div class="col-md-6">
                <base-input
                  name="Email Usuario"
                  placeholder="Email Usuario"
                  prepend-icon="ni ni-email-83"
                  rules="required"
                  v-model="email_usuario"
                >
                </base-input>
              </div>
              <!--<div class="col-md-6">
                <base-input
                  name="Password Usuario"
                  placeholder="Password Usuario"
                  prepend-icon="ni ni-key-25"
                  rules="required"
                  type="password"
                  :min="8"
                  v-model="contrasenia"
                >
                </base-input>
              </div>-->

              <div class="input-group col-md-6">
                <input
                  class="form-control"
                  aria-label="Example text with button addon"
                  aria-describedby="button-addon1"
                  name="Password Usuario"
                  placeholder="Password Usuario"
                  prepend-icon="ni ni-key-25"
                  rules="required"
                  :type="type_password"
                  :min="8"
                  v-model="contrasenia"
                />

                <div class="input-group-prepend">
                  <base-button
                    type="default"
                    @click="mostrarContrasenia()"
                    style="height: 3rem"
                    size="sm"
                    ><i class="ni ni-lock-circle-open"></i
                  ></base-button>
                </div>
              </div>
            </div>

            <div class="text-right" style="margin-top: 1rem">
              <base-button type="danger" @click="closeModalAddUsuario()"
                >Cancelar</base-button
              >
              <base-button
                type="primary"
                @click="insertNewUsuario()"
                native-type="submit"
                >Guardar</base-button
              >
            </div>
          </form>
        </validation-observer>
      </modal>

      <modal :show.sync="modalUpdateInfoUsuario">
        <validation-observer v-slot="{ handleSubmit }" ref="formValidator">
          <form
            class="needs-validation"
            @submit.prevent="handleSubmit(firstFormSubmit)"
          >
            <div class="form-row">
              <div class="col-md-6">
                <base-input
                  name="Nombres Usuario"
                  placeholder="Nombres Usuario"
                  prepend-icon="ni ni-single-02"
                  rules="required"
                  v-model="nombres"
                >
                </base-input>
              </div>
              <div class="col-md-6">
                <base-input
                  prepend-icon="ni ni-single-02"
                  name="Apellidos Usuario"
                  placeholder="Apellidos Usuario"
                  rules="required"
                  v-model="apellido"
                >
                </base-input>
              </div>
            </div>

            <div class="form-row" style="margin-top: 0.5rem">
              <div class="col-md-6">
                <base-input
                  name="Cedula / DNI"
                  placeholder="Cedula / DNI"
                  prepend-icon="ni ni-badge"
                  rules="required"
                  v-model="cedula"
                >
                </base-input>
              </div>
              <div class="col-md-6">
                <base-input
                  prepend-icon="ni ni-mobile-button"
                  name="Telefono Usuario"
                  rules="required"
                  type="tel"
                  placeholder="Telefono Usuario"
                  v-model="telefono"
                >
                </base-input>
              </div>
            </div>

            <div class="form-row" style="margin-top: 0.5rem">
              <div class="col-md-6">
                <base-input
                  name="Email Usuario"
                  placeholder="Email Usuario"
                  prepend-icon="ni ni-email-83"
                  rules="required"
                  :disabled="true"
                  v-model="email_usuario"
                >
                </base-input>
              </div>
              <div class="col-md-6">
                <el-select
                  v-model="mSelectEstadoUsuario"
                  placeholder="Estado Usuario"
                  style="width: 100%"
                >
                  <el-option :key="1" label="ACTIVO" :value="1"> </el-option>
                  <el-option :key="0" label="INACTIVO" :value="0"> </el-option>
                </el-select>
              </div>
            </div>

            <div class="text-right" style="margin-top: 1rem">
              <base-button type="danger" @click="closeModalUpdateInfoUsuario()"
                >Cancelar</base-button
              >
              <base-button
                type="primary"
                @click="updateInfoUsuario()"
                native-type="submit"
                >Guardar</base-button
              >
            </div>
          </form>
        </validation-observer>
      </modal>

      <modal :show.sync="modalUpdatePassword">
        <h5 slot="header" class="modal-title" id="modal-title-default">
          {{
            objRowSelectUser != null
              ? objRowSelectUser.nombres + " " + objRowSelectUser.apellido
              : "S/N"
          }}
        </h5>

        <validation-observer v-slot="{ handleSubmit }" ref="formValidator">
          <form
            class="needs-validation"
            @submit.prevent="handleSubmit(firstFormSubmit)"
          >
            <div class="form-row" style="margin-top: 0.5rem">
              <div class="col-md-12">
                <base-input
                  name="Email Usuario"
                  placeholder="Email Usuario"
                  prepend-icon="ni ni-email-83"
                  rules="required"
                  v-model="email_usuario"
                  :disabled="true"
                >
                </base-input>
              </div>
            </div>

            <div class="form-row" style="margin-top: 0.5rem">
              <div class="col-md-12">
                <base-input
                  name="Password Usuario"
                  placeholder="Password Usuario"
                  prepend-icon="ni ni-key-25"
                  rules="required"
                  type="password"
                  v-model="contrasenia"
                >
                </base-input>
              </div>
            </div>

            <div class="text-right" style="margin-top: 1rem">
              <base-button type="danger" @click="closeModalUpdatePassword()"
                >Cancelar</base-button
              >
              <base-button
                type="primary"
                @click="updateInfoUsuario()"
                native-type="submit"
                >Guardar</base-button
              >
            </div>
          </form>
        </validation-observer>
      </modal>

      <modal :show.sync="modalPermisosUsuario">
        <h5 slot="header" class="modal-title" id="modal-title-default">
          {{
            objRowSelectUser != null
              ? objRowSelectUser.nombres + " " + objRowSelectUser.apellido
              : "S/N"
          }}
        </h5>

        <form class="needs-validation">
          <div class="form-row" style="margin-top: 0.5rem">
            <div class="col-md-6">
              <el-checkbox border v-model="activeMercado"
                >OPC Procedencia - Sector</el-checkbox
              >
            </div>
            <div class="col-md-6">
              <el-checkbox border v-model="activeLote">OPC PILA</el-checkbox>
            </div>
          </div>

          <div class="form-row" style="margin-top: 0.5rem">
            <div class="col-md-6">
              <el-checkbox border v-model="activeHistorial"
                >OPC HISTORIAL</el-checkbox
              >
            </div>
            <div class="col-md-6">
              <el-checkbox border v-model="activeDespacho"
                >OPC DESPACHO</el-checkbox
              >
            </div>
          </div>

          <div class="form-row" style="margin-top: 0.5rem">
            <div class="col-md-6">
              <el-checkbox border v-model="activeUsuarios"
                >OPC USUARIOS</el-checkbox
              >
            </div>
            <div class="col-md-6">
              <el-checkbox border v-model="activeReporte"
                >OPC REPORTE</el-checkbox
              >
            </div>
          </div>

          <div class="form-row" style="margin-top: 0.5rem">
            <div class="col-md-6">
              <el-checkbox border v-model="activeInsumo"
                >OPC INSUMOS</el-checkbox
              >
            </div>
            <div class="col-md-6">
              <el-checkbox border v-model="activeRecordatorio"
                >OPC RECORDATORIO</el-checkbox
              >
            </div>
          </div>

          <div class="form-row" style="margin-top: 0.5rem">
            <div class="col-md-6">
              <el-checkbox border v-model="btn_tabla_mercados"
                >BTN TABLA Procedencia/S
              </el-checkbox>
            </div>
            <div class="col-md-6">
              <el-checkbox border v-model="btn_tabla_lotes"
                >BTN TABLA PILAS</el-checkbox
              >
            </div>
          </div>

          <div class="form-row" style="margin-top: 0.5rem">
            <div class="col-md-6">
              <el-checkbox border v-model="btn_tabla_insumos"
                >BTN TABLA INSUMOS</el-checkbox
              >
            </div>
            <div class="col-md-6">
              <el-checkbox border v-model="btn_tabla_despacho"
                >BTN TABLA DESPACHO</el-checkbox
              >
            </div>
          </div>

          <div class="form-row" style="margin-top: 0.5rem">
            <div class="col-md-6">
              <el-checkbox border v-model="btn_tabla_h_lotes"
                >BTN TABLA H - PILAS</el-checkbox
              >
            </div>

            <div class="col-md-6">
              <el-checkbox border v-model="active_entrada"
                >OPC ENTRADA</el-checkbox
              >
            </div>

            <div class="col-md-6">
              <el-checkbox border v-model="active_estadistico_lote"
                >OPC ESTADISTICO</el-checkbox
              >
            </div>

            <div class="col-md-6">
              <el-checkbox border v-model="active_tabla_entrada"
                >OPC TABLA ENTRADA</el-checkbox
              >
            </div>

          </div>

          <div class="text-right" style="margin-top: 1rem">
            <base-button type="danger" @click="closeModalPermisosUsuario()"
              >Cancelar</base-button
            >
            <base-button type="primary" @click="updatePermisosUsuario()"
              >Guardar</base-button
            >
          </div>
        </form>
      </modal>
    </base-header>
  </div>
</template>
<script>
import flatPicker from "vue-flatpickr-component";
import { getBase64LogoReportes } from "../util/logoReport";
import { convertSecondtoTimeString } from "../util/fechas";
import {
  validarNumeroCelular,
  validarCedula,
  validarCorreoElectronico,
} from "../util/validaciones";
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
  Tag,
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
    [Tag.name]: Tag,
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
      mListUsuarios: [],
      loadingUsuarios: false,
      tableColumnsUsuarios: [
        {
          prop: "email_usuario",
          label: "EMAIL",
          minWidth: 240,
        },
        {
          prop: "nombres",
          label: "NOMBRES",
          minWidth: 230,
        },
        {
          prop: "apellido",
          label: "APELLIDOS",
          minWidth: 230,
        },
        {
          prop: "cedula",
          label: "CEDULA / DNI",
          minWidth: 180,
        },
        {
          prop: "telefono",
          label: "TELEFONO",
          minWidth: 180,
        },
      ],
      token: this.$cookies.get("token"),
      modalAddUsuario: false,
      modalUpdatePassword: false,
      modalUpdateInfoUsuario: false,
      email_usuario: null,
      nombres: null,
      apellido: null,
      cedula: null,
      telefono: null,
      contrasenia: null,
      objRowSelectUser: null,
      mSelectEstadoUsuario: null,
      modalPermisosUsuario: false,
      activeMercado: false,
      activeLote: false,
      activeHistorial: false,
      activeDespacho: false,
      activeReporte: false,
      activeNotificacion: false,
      activeRecordatorio: false,
      activeUsuarios: false,
      activeInsumo: false,

      btn_tabla_mercados: false,
      btn_tabla_lotes: false,
      btn_tabla_insumos: false,
      btn_tabla_h_lotes: false,
      btn_tabla_despacho: false,
      active_estadistico_lote: false,
      active_entrada:false,
      active_tabla_entrada:false,
      type_password: "password",
    };
  },
  methods: {
    showModalPermisosUsuario(item) {
      this.objRowSelectUser = item;
      this.activeMercado = item.activeMercado == 1 ? true : false;
      this.activeLote = item.activeLote == 1 ? true : false;
      this.activeHistorial = item.activeHistorial == 1 ? true : false;
      this.activeDespacho = item.activeDespacho == 1 ? true : false;
      this.activeReporte = item.activeReporte == 1 ? true : false;
      this.activeNotificacion = item.activeNotificacion == 1 ? true : false;
      this.activeRecordatorio = item.activeRecordatorio == 1 ? true : false;
      this.activeUsuarios = item.activeUsuarios == 1 ? true : false;
      this.activeInsumo = item.activeInsumo == 1 ? true : false;
      this.modalPermisosUsuario = true;

      this.btn_tabla_mercados = item.active_options_mercado == 1 ? true : false;
      this.btn_tabla_lotes = item.active_options_lote == 1 ? true : false;
      this.btn_tabla_insumos = item.active_options_insumo == 1 ? true : false;
      this.btn_tabla_h_lotes =
        item.active_options_historial_lote == 1 ? true : false;
      this.btn_tabla_despacho =
        item.active_options_despacho == 1 ? true : false;

      this.active_estadistico_lote =
        item.active_estadistico_lote == 1 ? true : false;

      this.active_entrada = item.activeEntrada == 1 ? true :  false
      this.active_tabla_entrada = item.active_options_Entrada == 1 ? true :  false
    },
    closeModalPermisosUsuario() {
      this.objRowSelectUser = null;
      this.modalPermisosUsuario = false;
      this.activeMercado = false;
      this.activeLote = false;
      this.activeHistorial = false;
      this.activeDespacho = false;
      this.activeReporte = false;
      this.activeNotificacion = false;
      this.activeRecordatorio = false;
      this.activeUsuarios = false;
      this.activeInsumo = false;
    },
    showModalUpdateInfoUsuario(item) {
      this.objRowSelectUser = item;
      this.email_usuario = item.email_usuario;
      this.nombres = item.nombres;
      this.apellido = item.apellido;
      this.cedula = item.cedula;
      this.telefono = item.telefono;
      this.mSelectEstadoUsuario = item.estado;
      //console.log(this.objRowSelectUser)
      this.modalUpdateInfoUsuario = true;
    },
    closeModalUpdateInfoUsuario() {
      this.modalUpdateInfoUsuario = false;
    },
    showModalUpdatePassword(item) {
      this.objRowSelectUser = item;
      this.email_usuario = item.email_usuario;
      this.modalUpdatePassword = true;
    },
    closeModalUpdatePassword() {
      this.modalUpdatePassword = false;
    },
    clearModalUpdatePassword() {
      this.objRowSelectUser = null;
    },
    clearModalUpdateInforPassword() {
      this.email_usuario = null;
      this.nombres = null;
      this.apellido = null;
      this.cedula = null;
      this.telefono = null;
      this.mSelectEstadoUsuario = null;
    },
    showModalAddUsuario() {
      this.modalAddUsuario = true;
    },
    closeModalAddUsuario() {
      this.modalAddUsuario = false;
    },
    clearModalAddUsuario() {
      this.email_usuario = null;
      this.nombres = null;
      this.apellido = null;
      this.cedula = null;
      this.telefono = null;
      this.contrasenia = null;
    },
    async readUsuarioALL() {
      this.loadingUsuarios = true;
      this.mListUsuarios = [];
      try {
        var datos = await this.$axios.post(
          process.env.baseUrl + "/read_usuarios",
          { token: this.token }
        );

        if (datos.data.status_code == 200) {
          /*Notification.success({
              title: "Panel Salidas",
              message: "Datos consultados con éxito",
            });*/
          this.mListUsuarios.push(...datos.data.datos);
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
      this.loadingUsuarios = false;
    },
    showNotificationError(title, msm) {
      Notification.error({
        title: title,
        message: msm,
      });
    },
    async insertNewUsuario() {
      if (
        this.email_usuario != null &&
        this.nombres != null &&
        this.apellido != null &&
        this.cedula != null &&
        this.telefono != null &&
        this.contrasenia != null
      ) {
        try {
          if (validarCedula(this.cedula)) {
            if (validarCorreoElectronico(this.email_usuario)) {
              if (validarNumeroCelular(this.telefono)) {
                if (this.contrasenia.length >= 8) {
                  var datos = await this.$axios.post(
                    process.env.baseUrl + "/create_user",
                    {
                      email_usuario: this.email_usuario,
                      nombres: this.nombres,
                      apellido: this.apellido,
                      cedula: this.cedula,
                      telefono: this.telefono,
                      contrasenia: this.contrasenia,
                    }
                  );

                  if (datos.data.status_code == 200) {
                    this.closeModalAddUsuario();
                    this.clearModalAddUsuario();
                    this.readUsuarioALL();
                  } else {
                    this.showNotificationError("Nuevo Usuario", datos.data.msm);
                  }
                } else {
                  this.showNotificationError(
                    "Contraseña Usuario",
                    "Contraseña no válido (mínimo 8 caracteres)"
                  );
                }
              } else {
                this.showNotificationError(
                  "Teléfono Usuario",
                  "Número Teléfonico no válido"
                );
              }
            } else {
              this.showNotificationError("Email Usuario", "Email no es válido");
            }
          } else {
            this.showNotificationError(
              "Cedula Usuario",
              "Cedula o DNI no válido"
            );
          }
        } catch (error) {
          this.showNotificationError("Nuevo Usuario", error.toString());
        }
      }
    },
    async updatePasswordUsuario() {
      if (this.email_usuario != null || this.contrasenia != null) {
        if (this.contrasenia.length >= 8) {
          try {
            var datos = await this.$axios.put(
              process.env.baseUrl + "/update_password",
              {
                token: this.token,
                email: this.email_usuario,
                pass: this.contrasenia,
              }
            );

            console.log(datos.data);

            if (datos.data.status_code == 200) {
              this.closeModalUpdatePassword();
              this.clearModalUpdatePassword();
              this.clearModalAddUsuario();
              this.readUsuarioALL();
            } else {
              this.showNotificationError("Update Password", datos.data.msm);
            }
          } catch (error) {
            this.showNotificationError("Update Password", error.toString());
          }
        } else {
          this.showNotificationError(
            "Contraseña Usuario",
            "Contraseña no válido (mínimo 8 caracteres)"
          );
        }
      }
    },
    async updateInfoUsuario() {
      if (
        this.email_usuario != null &&
        this.nombres != null &&
        this.apellido != null &&
        this.cedula != null &&
        this.telefono != null &&
        this.mSelectEstadoUsuario != null
      ) {
        if (validarNumeroCelular(this.telefono)) {
          try {
            var datos = await this.$axios.put(
              process.env.baseUrl + "/update_info_usuario",
              {
                token: this.token,
                email: this.email_usuario,
                nombres: this.nombres,
                apellido: this.apellido,
                cedula: this.cedula,
                telefono: this.telefono,
                estado: this.mSelectEstadoUsuario,
              }
            );

            console.log(datos.data);

            if (datos.data.status_code == 200) {
              this.closeModalUpdateInfoUsuario();
              this.clearModalUpdateInforPassword();
              this.readUsuarioALL();
            } else {
              this.showNotificationError("UPDATE Usuario", datos.data.msm);
            }
          } catch (error) {
            this.showNotificationError("UPDATE Usuario", error.toString());
          }
        } else {
          this.showNotificationError(
            "UPDATE Usuario",
            "Número Teléfonico no válido"
          );
        }
      } else {
        console.log("DATOS VACIOS");
      }
    },
    async updatePermisosUsuario() {
      try {
        var datos = await this.$axios.put(
          process.env.baseUrl + "/update_permisos_usuario",
          {
            token: this.token,
            activeMercado: this.activeMercado == true ? 1 : 0,
            activeLote: this.activeLote == true ? 1 : 0,
            activeHistorial: this.activeHistorial == true ? 1 : 0,
            activeDespacho: this.activeDespacho == true ? 1 : 0,
            activeReporte: this.activeReporte == true ? 1 : 0,
            activeNotificacion: this.activeNotificacion == true ? 1 : 0,
            activeRecordatorio: this.activeRecordatorio == true ? 1 : 0,
            activeUsuarios: this.activeUsuarios == true ? 1 : 0,
            activeInsumo: this.activeInsumo == true ? 1 : 0,
            email: this.objRowSelectUser.email_usuario,
            btn_tabla_mercados: this.btn_tabla_mercados == true ? 1 : 0,
            btn_tabla_lotes: this.btn_tabla_lotes == true ? 1 : 0,
            btn_tabla_insumos: this.btn_tabla_insumos == true ? 1 : 0,
            btn_tabla_h_lotes: this.btn_tabla_h_lotes == true ? 1 : 0,
            btn_tabla_despacho: this.btn_tabla_despacho == true ? 1 : 0,
            active_estadistico_lote:
              this.active_estadistico_lote == true ? 1 : 0,
            active_entrada:this.active_entrada == true ? 1 : 0,
            active_tabla_entrada: this.active_tabla_entrada == true ? 1 : 0,  
          }
        );

        console.log(datos.data);

        if (datos.data.status_code == 200) {
          this.closeModalPermisosUsuario();
          this.readUsuarioALL();
        } else {
          this.showNotificationError("PERMISOS Usuario", datos.data.msm);
        }
      } catch (error) {
        this.showNotificationError("PERMISOS Usuario", error.toString());
      }
    },
    mostrarContrasenia() {
      if (this.type_password == "password") {
        this.type_password = "text";
      } else {
        this.type_password = "password";
      }
    },
  },
  mounted() {
    this.readUsuarioALL();
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
