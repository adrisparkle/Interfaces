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
        url: '/CostCenters/Paralelo/',
        propsToSearch: ['PrcCode', 'PrcName', 'U_Sigla', 'U_Materia', 'U_PeriodoPARALELO'],
        tableColumns: [
          {
            prop: 'PrcCode',
            label: '#',
            minWidth: 100
          },
          {
            prop: 'PrcName',
            label: 'Nombre',
            minWidth: 150
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
            prop: 'U_PeriodoPARALELO',
            label: 'Periodo',
            minWidth: 100
          },
          {
            prop: 'U_Sigla',
            label: 'Sigla',
            minWidth: 100
          },
          {
            prop: 'U_Materia',
            label: 'Materia',
            minWidth: 200
          },
          {
            prop: 'U_Paralelo',
            label: 'Paralelo',
            minWidth: 100
          },
          {
            prop: 'U_ModalidadPARALELO',
            label: 'Modalidad',
            minWidth: 100
          },
          {
            prop: 'U_EstadoParalelo',
            label: 'Estado',
            minWidth: 100
          },
          {
            prop: 'U_NivelParalelo',
            label: 'Nivel',
            minWidth: 100
          },
          {
            prop: 'U_TipoParalelo',
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
