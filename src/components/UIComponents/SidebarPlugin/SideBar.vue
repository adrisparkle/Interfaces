<template>
  <div :class="sidebarClasses"
       :data-background-color="backgroundColor"
       :data-active-color="activeColor">
      <div class="logo">
        <a class="simple-text logo-mini">
          <div class="logo-img">
            <img class="logo-ucb" :src="logo" alt="">
          </div>
        </a>
      </div>
    <div class="sidebar-wrapper" ref="sidebarScrollArea">
      <slot>
      </slot>
      <ul :class="navClasses">
        <slot name="links">
          
          <sidebar-item v-for="(link, index) in sidebarLinks"
                        :key="link.name + index"
                        :link="link">
            <sidebar-item v-for="(subLink, index) in link.children"
                          :key="subLink.name + index"
                          :link="subLink">
            </sidebar-item>
          </sidebar-item>
        </slot>
      </ul>
    </div>
  </div>
</template>
<script>
  export default {
    props: {
      title: {
        type: String,
        default: 'static/img/titulo_ucb.png'
      },
      type: {
        type: String,
        default: 'sidebar',
        validator: (value) => {
          let acceptedValues = ['sidebar', 'navbar']
          return acceptedValues.indexOf(value) !== -1
        }
      },
      backgroundColor: {
        type: String,
        default: 'white',
        validator: (value) => {
          let acceptedValues = ['white', 'brown', 'black', 'darkblue']
          return acceptedValues.indexOf(value) !== -1
        }
      },
      activeColor: {
        type: String,
        default: 'darkblue',
        validator: (value) => {
          let acceptedValues = ['primary', 'info', 'success', 'warning', 'danger', 'darkblue']
          return acceptedValues.indexOf(value) !== -1
        }
      },
      logo: {
        type: String,
        default: 'static/img/colores-horizontal-LP.jpg'
      },
      sidebarLinks: {
        type: Array,
        default: () => []
      }
    },
    computed: {
      sidebarClasses () {
        if (this.type === 'sidebar') {
          return 'sidebar'
        } else {
          return 'collapse navbar-collapse off-canvas-sidebar'
        }
      },
      navClasses () {
        if (this.type === 'sidebar') {
          return 'nav'
        } else {
          return 'nav navbar-nav'
        }
      }
    },
    methods: {
      async initScrollBarAsync () {
        await import('perfect-scrollbar/dist/css/perfect-scrollbar.css')
        const PerfectScroll = await import('perfect-scrollbar')
        PerfectScroll.initialize(this.$refs.sidebarScrollArea)
      }
    },
    mounted () {
      this.initScrollBarAsync()
    },
    beforeDestroy () {
      if (this.$sidebar.showSidebar) {
        this.$sidebar.showSidebar = false
      }
    }
  }

</script>
<style scoped>
  @media (min-width: 992px) {
    .navbar-search-form-mobile,
    .nav-mobile-menu{
      display: none;
    }
  }
  .logo-img{
    background: transparent!important;
  }
  .logo{
    padding: 0px 0px;
  }
  .logo-ucb{
    width: 230px;
    align-self: center;
    align-content: center;
  }
  .sidebar .logo .logo-img img, .off-canvas-sidebar .logo .logo-img img {
    max-width: 500px;
  }
  .color-user{
    color: #0000ff;
  }
</style>
