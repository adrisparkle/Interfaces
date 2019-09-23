<template>
  <div class="row">
    <div class="card">
      <data-tables v-bind="{url, propsToSearch, tableColumns,pagination,actions}">
      </data-tables>
    </div>
  </div>
</template>
<script>
  import Vue from 'vue'
  import {Tooltip} from 'element-ui'
  Vue.use(Tooltip)
  export default {
    data () {
      return {
        actions: false,
        url: '/CostCenters/Proyectos/',
        propsToSearch: ['PrjCode', 'PrjName', 'U_ModalidadProy', 'U_Sucursal', 'U_Tipo'],
        tableColumns: [
          {
            prop: 'PrjCode',
            label: '#',
            minWidth: 80
          },
          {
            prop: 'PrjName',
            label: 'Nombre  ',
            minWidth: 100
          },
          {
            prop: 'ValidFrom',
            label: 'Valido Desde',
            minWidth: 120
          },
          {
            prop: 'ValidTo',
            label: 'Valido Hasta',
            minWidth: 120
          },
          {
            prop: 'U_ModalidadProy',
            label: 'Modalidad',
            minWidth: 100
          },
          {
            prop: 'U_Sucursal',
            label: 'Sucursal',
            minWidth: 100
          },
          {
            prop: 'U_Tipo',
            label: 'Tipo',
            minWidth: 100
          }
        ],
        pagination: {
          perPage: 5,
          currentPage: 1,
          perPageOptions: [5, 10, 20],
          total: 0
        }
      }
    }
  }
</script>
<style>
</style>
