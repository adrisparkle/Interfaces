<template>
  <div>
      <div class="card-header">
      </div>
      <div class="card-content row">
        <div class="col-sm-5">
          <el-select
            class="select-default"
            v-model="pagination.perPage"
            placeholder="Per page">
            <el-option
              class="select-default"
              v-for="item in pagination.perPageOptions"
              :key="item"
              :label="item"
              :value="item">
            </el-option>
          </el-select>
        </div>
        <div class="col-sm-7">
          <div class="pull-right">
            <div class="row">
              <div class="col-sm-2">
                <label style="vertical-align: middle;">
                  Buscar
                </label>
              </div>
              <div class="col-sm-8">
                <input type="search" class="form-control input-sm" placeholder="Criterio de busqueda" v-model="searchQuery" aria-controls="datatables">
              </div>
              <div class="col-sm-2">
                <el-tooltip class="item" effect="dark" content="Exportar en formato Excel" placement="top-start">
                  <a class="btn btn-icon btn-success pull-right btn-fill" @click="exportExcel()">
                    <i class="fa fa-file-excel fa-lg" style=""></i>
                  </a>
                </el-tooltip>
              </div>
            </div>
          </div>
        </div>
        <div class="col-sm-12">
          <el-table class="table-striped"
                    :data="queriedData"
                    border
                    style="width: 100%">
            <el-table-column v-for="column in tableColumns"
                             :key="column.label"
                             :min-width="column.minWidth"
                             :formatter="cellValueRenderer"
                             :prop="column.prop"
                             :label="column.label">
            </el-table-column>
            <el-table-column v-if="actions"
              :min-width="80"
              fixed="right"
              label="Actions">
              <template slot-scope="props">
                <slot name="buttons" :queriedData="queriedData" :index="props.$index" :handleEdit="handleEdit" :handleDelete="handleDelete">
                </slot>
              </template>
            </el-table-column>
          </el-table>
        </div>
        <div class="col-sm-6 pagination-info">
          <p class="category">Mostrando {{from + 1}} a {{to}} de {{pagination.total}} registros</p>
        </div>
        <div class="col-sm-6">
          <p-pagination class="pull-right"
                        v-model="pagination.currentPage"
                        :per-page="pagination.perPage"
                        :total="total">
          </p-pagination>
        </div>
      </div>
  </div>
</template>
<script>
  import Vue from 'vue'
  import {Table, TableColumn, Select, Option} from 'element-ui'
  import PPagination from 'src/components/UIComponents/Pagination.vue'
  import { mapState } from 'vuex'

  Vue.use(Table)
  Vue.use(TableColumn)
  Vue.use(Select)
  Vue.use(Option)
  export default{
    components: {
      PPagination
    },
    computed: {
      pagedData () {
        return this.tableData.slice(this.from, this.to)
      },
      ...mapState({
        tableData: state => state.crud.data
      }),
      dataResult () {
        let result = this.tableData
          .filter((row) => {
            let isIncluded = true
            let all = ''
            for (let key of this.propsToSearch) {
              let rowValue = (row[key] == null ? '' : row[key]).toString()
              rowValue = rowValue.toUpperCase().replace(' ', '')
              all += rowValue
            }
            let separateStr = this.searchQuery.toUpperCase().split(' ')
            separateStr.forEach(function (word) {
              if (all.includes && !all.includes(word)) {
                isIncluded = false
              }
            })
            return isIncluded
          })
        return result
      },
      queriedData () {
        if (!this.searchQuery) {
          this.pagination.total = this.tableData.length
          return this.pagedData
        }
        let result = this.dataResult
        this.pagination.total = result.length

        return result.slice(this.from, this.to)
      },
      to () {
        let highBound = this.from + this.pagination.perPage
        if (this.total < highBound) {
          highBound = this.total
        }
        return highBound
      },
      from () {
        return this.pagination.perPage * (this.pagination.currentPage - 1)
      },
      total () {
        this.pagination.total = this.tableData.length
        return this.dataResult.length
      }
    },
    props: {
      pagination: {
        type: Object,
        default: function () {
          return {
            perPage: 5,
            currentPage: 1,
            perPageOptions: [5, 10, 25, 50],
            total: 0
          }
        }
      },
      actions: {
        type: Boolean,
        default: true
      },
      url: {
        type: String,
        required: true
      },
      propsToSearch: {
        type: Array,
        required: true
      },
      tableColumns: {
        type: Array,
        required: true
      },
      searchQueryProp: {
        type: String,
        default: ''
      }
    },
    data () {
      return {
        searchQuery: ''
      }
    },
    methods: {
      exportExcel () {
        this.downloadCSV()
      },
      convertArrayOfObjectsToCSV (args) {
        let result, ctr, keys, columnDelimiter, lineDelimiter, data
        data = args.data || null
        if (data == null || !data.length) {
          return null
        }
        columnDelimiter = args.columnDelimiter || ';'
        lineDelimiter = args.lineDelimiter || '\n'
        keys = Object.keys(data[0])
        result = ''
        result += keys.join(columnDelimiter)
        result += lineDelimiter
        data.forEach(function (item) {
          ctr = 0
          keys.forEach(function (key) {
            if (ctr > 0) result += columnDelimiter
            result += item[key]
            ctr++
          })
          result += lineDelimiter
        })
        return result
      },
      downloadCSV () {
        let data, filename, link
        let csv = this.convertArrayOfObjectsToCSV({
          data: this.dataResult
        })
        if (csv == null) return
        filename = 'export.csv'
        if (!csv.match(/^data:text\/csv/i)) {
          csv = 'data:text/csv;charset=utf-8,' + csv
        }
        data = encodeURI(csv)
        link = document.createElement('a')
        link.setAttribute('href', data)
        link.setAttribute('download', filename)
        link.click()
      },
      handleEdit (index) {
        const formData = {
          url: this.url,
          id: index
        }
        this.$store.commit('crud/editSetter', true)
        this.$store.dispatch('crud/find', formData)
      },
      handleDelete (index) {
        const formData = {
          url: this.url,
          id: index
        }
        this.$store.dispatch('crud/remove', formData)
      },
      cellValueRenderer (row, column, cellValue) {
        const isBool = typeof row[column.property] === 'boolean'
        return !isBool ? cellValue : String(cellValue)
      }
    },
    mounted () {
      this.$store.dispatch('crud/loadData', this.url)
      this.$store.commit('crud/formDataCleaner')
      this.$store.commit('crud/editSetter', false)
      this.searchQuery = this.searchQueryProp
    }
  }
</script>
<style>
</style>
