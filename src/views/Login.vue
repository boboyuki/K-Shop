<template>
  <div>
    <div class="login-page text-center">
      <form class="form-signin" @submit.prevent="signin">
        <h1 class="h3 mb-3 font-weight-normal">管理員登入</h1>
        <hr />
        <label for="inputEmail" class="sr-only">Email address</label>
        <input
          type="email"
          id="inputEmail"
          class="form-control"
          placeholder="Email address"
          required
          autofocus
          v-model="user.username"
        />
        <label for="inputPassword" class="sr-only">Password</label>
        <input
          type="password"
          id="inputPassword"
          class="form-control"
          placeholder="Password"
          required
          v-model="user.password"
        />
        <div class="checkbox mb-3">
          <label>
            <input type="checkbox" value="remember-me" /> 記住我
          </label>
        </div>
        <button class="btn btn-lg btn-primary btn-block rounded-pill" type="submit">登入</button>
      </form>
    </div>
  </div>
</template>

<script>
import Cookies from 'js-cookie'
export default {
  name: 'login',
  data () {
    return {
      user: {
        username: '',
        password: ''
      }
    }
  },
  methods: {
    signin () {
      const api = `${process.env.VUE_APP_APIPATH}/admin/signin`
      this.$http.post(api, this.user).then(res => {
        if (res.data.success) {
          const token = res.data.token
          Cookies.set('token', token)
          this.$router.push('/admin/products')
        }
      })
    }
  }
}
</script>
<style scoped>
.login-page {
  height: 100vh;
  width: 100%;
  justify-content: center;
  align-items: center;
  display: flex;
  background-size: cover;
  background-image: linear-gradient(to bottom, rgba(0, 195, 255, 0.5), transparent),url('https://store-images.s-microsoft.com/image/apps.3643.69575354529436354.9ab954db-c015-4868-b877-ddd52dc45f7a.0d2bc926-68e8-4aa4-9cd2-1bc73ca5d3ab?mode=scale&q=90&h=720&w=1280');
}
.form-signin {
  width: 100%;
  max-width: 450px;
  padding: 15px 25px;
  margin: auto 20px;
  background-color: rgba(245, 245, 245,0.7);
  border-radius: 15px;
  /* box-shadow: 0px 0px 0px 0px #f5f5f5; */
}
.form-signin .checkbox {
  font-weight: 400;
}
.form-signin .form-control {
  position: relative;
  box-sizing: border-box;
  height: auto;
  padding: 10px;
  font-size: 16px;
}
.form-signin .form-control:focus {
  z-index: 2;
}
.form-signin input[type="email"] {
  margin-bottom:10px;
  border-bottom-right-radius: 0;
  border-bottom-left-radius: 0;
}
.form-signin input[type="password"] {
  margin-bottom: 10px;
  border-top-left-radius: 0;
  border-top-right-radius: 0;
}
.bd-placeholder-img {
  font-size: 1.125rem;
  text-anchor: middle;
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
}

@media (min-width: 768px) {
  .bd-placeholder-img-lg {
    font-size: 3.5rem;
  }
}
</style>
