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
          <div class="cardTextoREstadistico">
            MODULO ESTADISTICO PILA
            <el-select
              placeholder="PILA"
              v-model="mSelectLote"
              collapse-tags
              style="margin-left: 1rem;"
            >
              <el-option
                v-for="item in mListLotes"
                :key="item.id_lote"
                :label="item.nombre_lote"
                :value="item.id_lote"
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
                @click="readEstadsitico()"
              >
                <span class="btn-inner--icon"
                  ><i class="el-icon-search"></i
                ></span>
              </base-button>
            </div>
          </div>
        </card>

        <card>
          <div class="row">
            <div class="col-xl-12">
              <card>
                <template slot="header">
                  <!-- Title -->
                  <h5 class="h3 mb-0">TEMPERATURA</h5>
                </template>
                <div class="chart">
                  <line-chart
                    ref="GraficaEstadisticaTemperatura"
                    :height="350"
                    :chart-data="EstaTemperatura.chartData"
                  >
                  </line-chart>
                </div>
              </card>
            </div>
          </div>

          <div class="row">
            <div class="col-xl-12">
              <card>
                <template slot="header">
                  <!-- Title -->
                  <h5 class="h3 mb-0">HUMEDAD</h5>
                </template>
                <div class="chart">
                  <line-chart ref="GraficaEstadisticaHumedad" :height="350" :chart-data="EstaHumedad.chartData">
                  </line-chart>
                </div>
              </card>
            </div>
          </div>

          <div class="row">
            <div class="col-xl-12">
              <card>
                <template slot="header">
                  <!-- Title -->
                  <h5 class="h3 mb-0">PH</h5>
                </template>
                <div class="chart">
                  <line-chart ref="GraficaEstadisticaPh" :height="350" :chart-data="EstaPh.chartData">
                  </line-chart>
                </div>
              </card>
            </div>
          </div>
        </card>
      </div>
    </base-header>
  </div>
</template>
<script>
import DoughnutChart from "~/components/argon-core/Charts/DoughnutChart";
import LineChart from "~/components/argon-core/Charts/LineChart";
import BarChart from "~/components/argon-core/Charts/BarChart";
import PieChart from "~/components/argon-core/Charts/PieChart";
import * as chartConfigs from "~/components/argon-core/Charts/config";
import StatsCard from "~/components/argon-core/Cards/StatsCard";
import { Charts } from "~/components/argon-core/Charts/config";
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

function randomScalingFactor() {
  return Math.round(Math.random() * 100);
}

export default {
  mixins: [clientPaginationMixin],
  layout: "DashboardLayout",
  components: {
    DoughnutChart,
    LineChart,
    BarChart,
    PieChart,
    StatsCard,
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
      mListLotes: [],
      mSelectLote: [],
      EstaTemperatura: {
        chartData: {
          labels: [],
          datasets: [
            {
              label: "DATO TEMP : ",
              data: [],
            },
          ],
        },
        extraOptions: chartConfigs.blueChartOptions,
      },
      EstaHumedad: {
        chartData: {
          labels: [
          ],
          datasets: [
            {
              label: "DATO HUM : ",
              data: [],
            },
          ],
        },
        extraOptions: chartConfigs.blueChartOptions,
      },
      EstaPh: {
        chartData: {
          labels: [
          ],
          datasets: [
            {
              label: "DATO PH : ",
              data: [],
            },
          ],
        },
        extraOptions: chartConfigs.blueChartOptions,
      },
    };
  },
  methods: {
    async readLoteAll() {
      this.mListLotes = [];
      try {
        var datos = await this.$axios.post(
          process.env.baseUrl + "/lote_all_user",
          { token: this.token }
        );

        this.mListLotes.push(...datos.data.datos);



      } catch (error) {
        console.log(error);
      }
    },
    async readEstadsitico() {


      this.EstaTemperatura.chartData.labels = []
      this.EstaHumedad.chartData.labels = []
      this.EstaPh.chartData.labels = []

      this.$refs.GraficaEstadisticaTemperatura.renderChart(
            [],
            []
          );

          this.$refs.GraficaEstadisticaHumedad.renderChart(
            [],
            []
          );

          this.$refs.GraficaEstadisticaPh.renderChart(
            [],
            []
          );
      var listaEstadistico = [];
      try {
        var datos = await this.$axios.get(
          process.env.baseUrl + "/readEstadisticoLote/"+this.mSelectLote,
          {}
        );

        listaEstadistico.push(...datos.data.datos);

        this.DibujandoGraficos(this.$refs.GraficaEstadisticaTemperatura,listaEstadistico,this.EstaTemperatura,1)
        this.DibujandoGraficos(this.$refs.GraficaEstadisticaHumedad,listaEstadistico,this.EstaHumedad,2)
        this.DibujandoGraficos(this.$refs.GraficaEstadisticaPh,listaEstadistico,this.EstaPh,3)
        
      } catch (error) {
        console.log(error);
      }
      console.log(listaEstadistico);
    },
    DibujandoGraficos(referencia_grafica,lista,grafica,bandera)
    {
      for (var i = 0; i < lista.length; i++) 
        {
          grafica.chartData.labels.push(
            lista[i].fechaHistorial
          );
        }

        //console.log(this.EstaTemperatura.chartData.labels)

        grafica.chartData.datasets[0].data = [];

        for (var i = 0; i < lista.length; i++) 
        {
          if(bandera == 1){
            grafica.chartData.datasets[0].data.push(parseFloat(lista[i].vTemperatura));
          }else if(bandera == 2){
            grafica.chartData.datasets[0].data.push(parseFloat(lista[i].vHumedad));
          }else {
            grafica.chartData.datasets[0].data.push(parseFloat(lista[i].vPh));
          }
        }

        if (referencia_grafica) {
          referencia_grafica.renderChart(
            grafica.chartData,
            grafica.extraOptions
          );
        }
    }
  },
  mounted() {
    this.readLoteAll();

    // GraficaEstadisticaHumedad
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

.cardTextoREstadistico {
  display: flex;
  align-items: center;
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
