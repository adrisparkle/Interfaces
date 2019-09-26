<template>
  <div class="wrapper">
    <loading :active.sync="this.$store.state.crud.isLoading"
             :can-cancel="false"
             :is-full-page="true"></loading>

    <side-bar type="sidebar">
      <div class="logo">
        <a class="simple-text logo-mini">
          <div class="logo-img">
            <img class="logo-ucb" :src="logo" alt="">
          </div>
        </a>
      </div>
      <user-menu></user-menu>
      <form class="navbar-form navbar-left navbar-search-form navbar-search-form-mobile" role="search">
        <div class="input-group">
          <span class="input-group-addon"><i class="fa fa-search"></i></span>
          <input type="text" value="" class="form-control" placeholder="Search...">
        </div>
      </form>
      <mobile-menu></mobile-menu>
   </side-bar>

    <div class="main-panel">
      <top-navbar></top-navbar>

      <dashboard-content>

      </dashboard-content>

      <!--content-footer></content-footer-->
    </div>
  </div>
</template>
<style lang="scss">

</style>
<script>
  import axios from 'axios'
  import Vue from 'vue'
  import TopNavbar from './TopNavbar.vue'
  import ContentFooter from './ContentFooter.vue'
  import DashboardContent from './Content.vue'
  import UserMenu from 'src/components/UIComponents/SidebarPlugin/UserMenu.vue'
  import MobileMenu from 'src/components/UIComponents/SidebarPlugin/MobileMenu.vue'
  // Import component
  import Loading from 'vue-loading-overlay'
  // Import stylesheet
  import 'vue-loading-overlay/dist/vue-loading.min.css'
  // Using axios for the example only
  /* import SideBar from '../UIComponents/SidebarPlugin'
  Vue.use(SideBar) */

  export default {
    components: {
      TopNavbar,
      ContentFooter,
      DashboardContent,
      UserMenu,
      Loading
    }
  }

</script>
