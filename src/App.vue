<template>
  <Navbar />
  <div class="container">
    <router-view v-show="showPage" @ready="onPageReady" :key="`${$route.path}${JSON.stringify($route.query)}`" />
    <AppSpinner v-show="!showPage" />
    <AppNotifications />
  </div>
</template>

<script>
import Navbar from '@/components/Navbar'
import { mapActions } from 'vuex'
import NProgress from 'nprogress'
import AppNotifications from '@/components/AppNotifications'

export default {
  name: 'App',
  components: {
    Navbar,
    AppNotifications
  },
  data() {
    return {
      showPage: false
    }
  },
  methods: {
    ...mapActions('auth', ['fetchAuthUser']),
    onPageReady() {
      this.showPage = true
      NProgress.done()
    }
  },
  created() {
    this.fetchAuthUser()
    NProgress.configure({
      showSpinner: false
    })
    this.$router.beforeEach(() => {
      this.showPage = false
      NProgress.start()
    })
  }
}
</script>

<style>
@import 'assets/style.css';
@import "~nprogress/nprogress.css";

#nprogress .bar {
  background: #57AD8D !important;
}
</style>