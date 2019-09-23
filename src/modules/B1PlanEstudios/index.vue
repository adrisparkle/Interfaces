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
        url: '/CostCenters/PlanDeEstudios/',
        propsToSearch: ['PrcCode', 'PrcName', 'U_NUM_INT_CAR', 'U_Nivel'],
        tableColumns: [
          {
            prop: 'PrcCode',
            label: '#',
            minWidth: 80
          },
          {
            prop: 'PrcName',
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
            prop: 'U_NUM_INT_CAR',
            label: 'Numero Interno',
            minWidth: 100
          },
          {
            prop: 'U_Nivel',
            label: 'Nivel',
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
