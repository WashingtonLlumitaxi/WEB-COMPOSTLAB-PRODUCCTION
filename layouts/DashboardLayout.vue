<template>
  <div class="wrapper">
    <notifications></notifications>
    <side-bar>
      <template slot-scope="props" slot="links">
        <sidebar-item
          translate="no"
          v-if="activeUsuarios"
          :link="{
            name: 'Usuarios',
            icon: 'ni ni-single-02 text-info',
            path: './usuario',
          }"
        >
        </sidebar-item>

        <sidebar-item
          translate="no"
          v-if="activeMercado"
          :link="{
            name: 'Procedencia - Sector',
            icon: 'ni ni-building text-success',
            path: './mercado',
          }"
        >
        </sidebar-item>

        <sidebar-item
          translate="no"
          v-if="activeInsumo"
          :link="{
            name: 'Insumos',
            icon: 'ni ni-box-2 text-default',
            path: './insumo',
          }"
        >
        </sidebar-item>

        <sidebar-item
          translate="no"
          v-if="activeEntrada"
          :link="{
            name: 'Entrada-Lote',
            icon: 'ni ni-delivery-fast',
            path: './entrada',
          }"
        >
        </sidebar-item>

        

        <sidebar-item
          translate="no"
          v-if="activeLote"
          :link="{
            name: 'Pilas',
            icon: 'ni ni-ungroup text-primary',
            path: './pila',
          }"
        >
        </sidebar-item>

        


        <sidebar-item
          translate="no"
          v-if="activeHistorial"
          :link="{
            name: 'Historial - Pilas',
            icon: 'ni ni-book-bookmark text-danger',
            path: './historial_lote',
          }"
        >
        </sidebar-item>

        

        <sidebar-item
          translate="no"
          v-if="active_estadistico_lote"
          :link="{
            name: 'Estadistica Pila',
            icon: 'ni ni-sound-wave text-primary',
            path: './EstadisticaLote',
          }"
        >
        </sidebar-item>
        

        <sidebar-item
          translate="no"
          v-if="activeDespacho"
          :link="{
            name: 'Despacho',
            icon: 'ni ni-delivery-fast text-primary',
            path: './despacho',
          }"
        >
        </sidebar-item>

        <sidebar-item translate="no"  :link="{
          name: 'Reportes',
          icon: 'ni ni-single-copy-04 text-warning',
        }">

          <!--<sidebar-item :link="{ name: 'Cant. Composta', path: '/' }" translate="no"/>
          <sidebar-item :link="{ name: 'Composta Mercado', path: '/' }" translate="no"/>-->
          <sidebar-item :link="{ name: 'Salidas Pila', path: '/rSalidas' }" translate="no"/>
          <sidebar-item :link="{ name: 'Actividad-Pila', path: '/rActividadLote' }" translate="no"/>
          <sidebar-item :link="{ name: 'Insumos Pila', path: '/rInsumoLote' }" translate="no"/>
          <sidebar-item :link="{ name: 'Historial-Pila', path: '/rHLote' }" translate="no"/> 
          <sidebar-item :link="{ name: 'Entrada-Lote', path: '/rEntrada' }" translate="no"/> 
          <sidebar-item :link="{ name: 'Composta Procedencia - Sector', path: '/rCompostMercado' }" translate="no"/> 
          <sidebar-item :link="{ name: 'Material Orgánico-Impropio ', path: '/rImpropioOrganico' }" translate="no"/>
          
        </sidebar-item>

      </template>
    </side-bar>
    <div class="main-content">
      <dashboard-navbar
        :type="$route.name == 'alternative' ? 'light' : 'default'"
      ></dashboard-navbar>

      <div @click="$sidebar.displaySidebar(false)">
        <nuxt></nuxt>
      </div>
    </div>
  </div>
</template>
<script>
/* eslint-disable no-new */
import PerfectScrollbar from "perfect-scrollbar";
import "perfect-scrollbar/css/perfect-scrollbar.css";

function hasElement(className) {
  return document.getElementsByClassName(className).length > 0;
}

function initScrollbar(className) {
  if (hasElement(className)) {
    new PerfectScrollbar(`.${className}`);
  } else {
    // try to init it later in case this component is loaded async
    setTimeout(() => {
      initScrollbar(className);
    }, 100);
  }
}

import DashboardNavbar from "~/components/layouts/argon/DashboardNavbar.vue";
import DashboardContent from "~/components/layouts/argon/Content.vue";
import jwt_decode from "jwt-decode";

export default {
  components: {
    DashboardNavbar,
    DashboardContent,
  },
  data() {
    return {
      activeMercado: 0,
      activeLote: 0,
      activeHistorial: 0,
      activeDespacho: 0,
      activeReporte: 0,
      activeNotificacion: 0,
      activeRecordatorio: 0,
      activeUsuarios: 0,
      activeInsumo:0,
      active_estadistico_lote:0,
      activeEntrada:0
    };
  },
  methods: {
    initScrollbar() {
      let isWindows = navigator.platform.startsWith("Win");
      if (isWindows) {
        initScrollbar("navbar-inner");
        initScrollbar("main-content");
        initScrollbar("sidenav");
      }
    },
    decodesJWT(){
      var token = this.$cookies.get("token")
      var data = jwt_decode(token).datosJWT
    
      console.log(data)
      
      this.activeMercado = data.activeMercado
      this.activeLote = data.activeLote
      this.activeHistorial = data.activeHistorial
      this.activeDespacho = data.activeDespacho
      this.activeReporte = data.activeReporte
      this.activeNotificacion = data.activeNotificacion
      this.activeRecordatorio = data.activeRecordatorio
      this.activeUsuarios = data.activeUsuarios
      this.activeInsumo = data.activeInsumo
      this.active_estadistico_lote = data.active_estadistico_lote
      this.activeEntrada = data.activeEntrada
    }
  },
  mounted() {
    this.initScrollbar()
    this.decodesJWT()
  },
};
</script>
<style lang="scss"></style>
