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
            REPORTE COMPOSTA PROCEDENCIA
            <el-select
              placeholder="Procedencia - Sector"
              v-model="mSelectMercado"
              multiple
              collapse-tags
              style="margin-left: 1rem;"
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
                @click="readCompostMercados()"
              >
                <span class="btn-inner--icon"
                  ><i class="el-icon-search"></i
                ></span>
              </base-button>

              <base-button
                icon
                type="danger"
                size="sm"
                v-if="mListInsumoLotes.length > 0"
                @click="exportPdfRSalidas()"
              >
                <span class="btn-inner--icon"
                  ><i class="ni ni-single-copy-04"></i
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
            :data="mListInsumoLotes"
            row-key="id"
            v-loading="loadingInsumoLotes"
            class="tablePanelControlProduccion"
            header-row-class-name="thead-dark"
            height="calc(100vh - 11rem)"
            style="width: 100%"
          >
            <el-table-column prop="nombre_mercado" label="Procedencia - Sector" width="480">
            </el-table-column>

            <el-table-column
              prop="totLotes"
              label="TOTAL. PILAS"
              width="200"
            >
            </el-table-column>

            <el-table-column prop="totalPeso" label="TOTAL PESO KG" width="300">
            </el-table-column>

            <el-table-column prop="cant_organica_mercado" label="TOTAL PESO SOBRANTE KG" width="300">
            </el-table-column>


            <div slot="empty"></div>
          </el-table>
        </card>
        .
      </div>
    </base-header>
  </div>
</template>
<script>

import pdfMake from "pdfmake/build/pdfmake.js";
import pdfFonts from "pdfmake/build/vfs_fonts.js";
import { getBase64LogoReportes } from "../util/logoReport";
pdfMake.vfs = pdfFonts.pdfMake.vfs;

import flatPicker from "vue-flatpickr-component";
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
      token: this.$cookies.get("token"),
      mListMercados: [],
      loadingInsumoLotes: false,
      mSelectMercado: [],
      mListInsumoLotes: [],
    };
  },
  methods: {
    async readMercadoAll() {
      this.mListMercados = [];
      try {
        var datos = await this.$axios.get(
          process.env.baseUrl + "/all_mercados_active",
        );

        this.mListMercados.push(...datos.data.datos);
      } catch (error) {
        console.log(error);
      }
    },
    async readCompostMercados() {
      this.mListInsumoLotes = [];
      this.loadingInsumoLotes = true;
      try {
        var datos = await this.$axios.post(
          process.env.baseUrl + "/compost_mercados",
          {
            token: this.token,
            mercados: this.mSelectMercado.length == 0 ? "*" : this.mSelectMercado,
          }
        );

        this.mListInsumoLotes.push(...datos.data.datos);
      } catch (error) {
        console.log(error);
      }
      this.loadingInsumoLotes = false;
    },
    async exportPdfRSalidas() {
      var resultadoString = [
        [
          // CodiVehiDispEven,HoraDispEven,DescRutaSali_m,NumeVuelSali_m,DescFrec,DescDispEvenList,LatiDispEven,LongDispEven
          {
            text: "Procedencia - Sector",
            fontSize: 8.5,
            bold: true,
            fillColor: "#039BC4",
            color: "white",
            alignment: "center",
          },
          {
            text: "TOT. PILAS",
            fontSize: 8.5,
            bold: true,
            fillColor: "#039BC4",
            color: "white",
            alignment: "center",
          },
          {
            text: "TOTAL PESO KG",
            fontSize: 8.5,
            bold: true,
            fillColor: "#039BC4",
            color: "white",
            alignment: "center",
          },
          {
            text: "TOTAL PESO SOBRANTE KG",
            fontSize: 8.5,
            bold: true,
            fillColor: "#039BC4",
            color: "white",
            alignment: "center",
          }
        ],
      ];

      //CodiVehiDispEven,HoraDispEven,DescRutaSali_m,NumeVuelSali_m,DescFrec,DescDispEvenList,LatiDispEven,LongDispEven

      for (var i = 0; i < this.mListInsumoLotes.length; i++) {
        var arrys = [
          {
            text: this.mListInsumoLotes[i].nombre_mercado,
            fontSize: 8.5,
            alignment: "center",
          },
          {
            text: this.mListInsumoLotes[i].totLotes,
            fontSize: 8.5,
            alignment: "center",
          },
          {
            text: this.mListInsumoLotes[i].totalPeso,
            fontSize: 8.5,
            alignment: "center",
          },
          {
            text: this.mListInsumoLotes[i].cant_organica_mercado,
            fontSize: 8.5,
            alignment: "center",
          }
        ];
        resultadoString.push(arrys);
      }

      /**
 * function (currentPage, pageCount, pageSize) {
    //"REPORTE INDICADORES DE CALIDAD \n Dir : Av Chasquis y Rio Guayllabamba (Ambato) Email : vigitracklatam@gmail.com \n Tel : 0995737084 - 032421698 Sitio Web : www.vigitrackecuador.com"
    return [
      {
        text: "REPORTE SALIDAS DETALLADAS",
        alignment: "center",
        fontSize: 16,bold:true
      },
      {
        text: "Dir : Av Chasquis y Rio Guayllabamba (Ambato) Email : vigitracklatam@gmail.com",
        alignment: "center",
        fontSize: 8
      },{
        text: "Tel : 0995737084 - 032421698 Sitio Web : www.vigitrackecuador.com",
        alignment: "center",
        fontSize: 8
      }
    ];
  }
 * ***/
      var docDefinition = {
        pageSize: "A4",
        pageMargins: [40, 80, 40, 60],
        header: {
          margin: 15,
          columns: [
            {
              image: getBase64LogoReportes(""),
              width: 190,
              height: 45,
              margin: [30, 0, 0, 0],
            },
            {
              layout: "noBorders",
              table: {
                widths: ["*"],
                body: [
                  [
                    {
                      text: "REPORTE COMPOSTA PROCEDENCIA - SECTOR",
                      alignment: "center",
                      fontSize: 16,
                      bold: true,
                    },
                  ],
                  [
                    {
                      text: "Dir :  5 de Junio y Primera Constituyente Email : dircomunicacion@gadmriobamba.gob.ec",
                      alignment: "center",
                      fontSize: 8,
                    },
                  ],
                  [
                    {
                      text: "Tel : 096 973 8255 Sitio Web : http://www.gadmriobamba.gob.ec/",
                      alignment: "center",
                      fontSize: 8,
                    },
                  ],
                ],
              },
            },
          ],
        },
        content: [
          {
            table: {
              // headers are automatically repeated if the table spans over multiple pages
              // you can declare how many rows should be treated as headers
              headerRows: 0,
              widths: [220, 90, 90,90],
              body: resultadoString,
            },
          },
        ],
      };
      /*var win = window.open("", "_blank");
pdfMake.createPdf(docDefinition).open({}, win);*/
      pdfMake.createPdf(docDefinition).download("RCM_" + Date.now());
    },
  },
  mounted() {
    this.readMercadoAll();
    this.readCompostMercados();
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
  height: calc(100vh - 10.5rem);
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
