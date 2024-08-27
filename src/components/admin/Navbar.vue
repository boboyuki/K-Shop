<template>
  <div>
    <nav class="navbar bg-primary fixed-top flex-md-nowrap p-0 shadow">
      <a class="navbar-brand col-sm-3 col-md-2 mr-0 text-white" href="#">跑跑管理員</a>
      <ul class="navbar-nav px-3">
        <li class="nav-item text-nowrap">
          <a class="nav-link text-white" href="#" @click.prevent="signout">
            <i class="fas fa-sign-out-alt d-2"></i>
          </a>
        </li>
      </ul>
    </nav>
  </div>
</template>
<script>
import Cookies from 'js-cookie'
export default {
  name: 'NavBar',
  methods: {
    signout () {
      const vm = this
      const url = `${process.env.VUE_APP_APIPATH}/logout`
      this.$http.post(url).then((res) => {
        if (res.data.success) {
          vm.$router.push('/')
          Cookies.remove('token')
        }
      })
    }
  }
}
</script>
