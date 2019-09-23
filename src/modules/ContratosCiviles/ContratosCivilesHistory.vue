<template>
  <div class="row">
    <div class="col-md-12 card">
      <template v-if="!showWizard">
        <data-tables v-bind="{url, propsToSearch, tableColumns,pagination}">
          <template slot="buttons" slot-scope="props">
            <el-tooltip class="item" effect="dark" content="Aprobar" placement="top-start">
              <a class="btn btn-simple btn-xs btn-icon btn-info" @click="initWizard(props.queriedData[props.index].Id)"><i class="fa fa-edit"></i></a>
            </el-tooltip>
          </template>
        </data-tables>
      </template>
      <template v-else>
        <button  class="btn btn-warning btn-fill btn-wd btn-back" style="margin-left: 80%" @click="showWizard= false">Volver a Historial</button>
        <div class="card card-wizard" id="wizardCard">
          <form-wizard shape="tab" ref="wizard"
                       @on-complete="wizardComplete"
                       error-color="#D32F2F"
                       color="#FFA000"
                       title="Asignacion de Centros de Responsabilidad"
                       subtitle="siga los siguientes pasos">

            <tab-content title="Comprobación"
                         icon="ti-check">
              <BeforeEndStep></BeforeEndStep>
            </tab-content>

            <tab-content title="Confirmación"
                         icon="ti-check">
              <EndStep :ToAprove="ToAprove"></EndStep>
            </tab-content>

            <button slot="prev" class="btn btn-default btn-fill btn-wd btn-back">Atras</button>
            <button slot="next" class="btn btn-info btn-fill btn-wd btn-next">Siguiente</button>
            <button slot="finish" class="btn btn-warning btn-fill btn-wd">Finalizar</button>
          </form-wizard>
        </div>
      </template>
    </div>
  </div>
</template>
<script>
  import axios from 'axios'
  import swal from 'sweetalert2'
  import {FormWizard, TabContent} from 'vue-form-wizard'
  import 'vue-form-wizard/dist/vue-form-wizard.min.css'
  import BeforeEndStep from './Steps/BeforeEndStep'
  import EndStep from './Steps/EndStep'
  import router from 'src/router/index'

  export default {
    data () {
      return {
        ToAprove: true,
        id: 0,
        url: '/ServContract/History/',
        formData: {
          FullName: null,
          SAPId: null,
          NIT: null,
          Document: null
        },
        showWizard: false,
        propsToSearch: ['Id', 'FullName', 'Category', 'SAPId', 'NIT'],
        tableColumns: [
          {
            prop: 'Id',
            label: '#',
            minWidth: 50
          },
          {
            prop: 'Branches',
            label: 'Regional',
            minWidth: 100
          },
          {
            prop: 'FileType',
            label: 'Tipo de Servicio',
            minWidth: 100
          },
          {
            prop: 'State',
            label: 'Estado',
            minWidth: 100
          },
          {
            prop: 'CreatedAt',
            label: 'Creado',
            minWidth: 100
          },
          {
            prop: 'SAPId',
            label: 'SAPId',
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
    },
    methods: {
      successMessage: function () {
        swal({
          title: `Buen trabajo!`,
          text: 'Se guardaron los cambios!',
          buttonsStyling: false,
          confirmButtonClass: 'btn btn-success btn-fill',
          type: 'success'
        })
      },
      errorMessage: function (text) {
        swal({
          title: `Ups!`,
          text: text,
          buttonsStyling: false,
          confirmButtonClass: 'btn btn-success btn-fill',
          type: 'error'
        })
      },
      send: function () {
        axios.post('civil/', this.formData)
          .then(response => {
            this.successMessage()
            this.formData.SAPId = null
            this.ResetForm()
          })
          .catch(error => {
            if (error.response.status === 401) {
              this.errorMessage('Su usuario no tiene permisos para usar este socio de negocio')
            }
          })
      },
      initWizard: function (id) {
        axios.get('ServContract/' + id, this.formData)
          .then(response => {
            console.log(response.data.State)
            if (response.data.State === 'ESPERANDO APROBACION') {
              this.$store.commit('civ/uploadedFilesIdSetter', id)
              this.$store.commit('civ/segmentoSetter', response.data.BranchesId)
              this.$store.commit('civ/tipoArchivoSetter', response.data.FileType)
              this.$store.dispatch('civ/uploadedFiles')
              this.showWizard = true
            } else {
              this.errorMessage('No se puede aprobar este proceso.')
            }
          })
          .catch()
      },
      wizardComplete () {
        router.go(0)
        router.push('/ContratosCivilesProcesos')
      }
    },
    components: {
      FormWizard,
      TabContent,
      BeforeEndStep,
      EndStep
    }
  }
</script>
<style>
  .vue-form-wizard .wizard-icon-circle.tab_shape {
    width: 100%;
    min-width: 100px;
    height: 40px;
    border: none;
    background-color: #1c3b6c;
    color: #fff;
    border-radius: 0;
  }
  .stepTitle{
    color: #1c3b6c;
  }
  .sa {
    width: 260px;
    height: 260px;
    padding: 26px;
    background-color: #fff;
    margin: 0 auto!important;
  }
  .sa-success {
    border-radius: 50%;
    border: 4px solid #A5DC86;
    box-sizing: content-box;
    height: 200px;
    padding: 0;
    position: relative;
    background-color: #fff;
    width: 200px;
  }
  .sa-success:after, .sa-success:before {
    background: #fff;
    content: '';
    height: 250px;
    position: absolute;
    transform: rotate(45deg);
    width: 140px;
    margin-top: 29px;
    margin-left: -2px;
  }
  .sa-success:before {
    border-radius: 40px 0 0 40px;
    width: 70px;
    height: 205px;
    top: 15px;
    left: -21px;
    transform-origin: 60px 60px;
    transform: rotate(-45deg);
  }
  .sa-success:after {
    border-radius: 0 120px 120px 0;
    left: 30px;
    top: -11px;
    transform-origin: 0 60px;
    transform: rotate(-45deg);
    animation: rotatePlaceholder 4.25s ease-in;
  }
  .sa-success-placeholder {
    border-radius: 50%;
    border: 4px solid rgba(76, 175, 80, 0.25);
    box-sizing: content-box;
    height: 200px;
    left: -4px;
    position: absolute;
    top: -4px;
    width: 200px;
    z-index: 2;
  }
  .sa-success-fix {
    background-color: #fff;
    height: 205px;
    left: 70px;
    position: absolute;
    top: 25px;
    transform: rotate(-45deg);
    width: 6px;
    z-index: 1;
  }
  .sa-success-tip, .sa-success-long {
    background-color: #8BC34A;
    border-radius: 2px;
    height: 5px;
    position: absolute;
    z-index: 2;
  }
  .sa-success-tip {
    left: 24px;
    top: 112px;
    transform: rotate(45deg);
    width: 73px;
    animation: animateSuccessTip .75s;
  }
  .sa-success-long {
    right: 15px;
    top: 96px;
    transform: rotate(-45deg);
    width: 119px;
    animation: animateSuccessLong .75s;
  }

  @keyframes animateSuccessTip {
    0%, 54% {
      width: 0;
      left: -3px;
      top: 83px;
    }
    70% {
      width: 112px;
      left: -11px;
      top: 98px;
    }
    84% {
      width: 49px;
      left: 46px;
      top: 120px;
    }
    100% {
      width: 73px;
      left: 24px;
      top: 112px;
    }
  }
  @keyframes animateSuccessLong {
    0%, 65% {
      width: 0;
      right: 45px;
      top: 50px;
    }
    84% {
      width: 136px;
      right: -1px;
      top: 88px;
    }
    100% {
      width: 119px;
      right: 15px;
      top: 96px;
    }
  }
  @keyframes rotatePlaceholder {
    0%, 5% {
      transform: rotate(-45deg);
    }
    100%, 12% {
      transform: rotate(-405deg);
    }
  }
</style>
