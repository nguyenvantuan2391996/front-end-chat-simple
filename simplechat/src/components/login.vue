<template>
  <div>
    <div class="container">
      <div class="row">
        <div class="col-md-6 col-md-offset-3">
          <div class="panel panel-login">
            <div class="panel-heading">
              <div class="row">
                <div class="col-xs-12">
                  <a href="/" class="active" id="login-form-link">Login</a>
                </div>
              </div>
              <hr>
            </div>
            <div class="panel-body">
              <div class="row">
                <div class="col-lg-12">
                  <form @submit.prevent="login" method="post">
                    <div class="form-group">
                      <input type="text" name="username" id="username" tabindex="1" class="form-control"
                             placeholder="Username" v-model="formLogin.username">
                    </div>
                    <div class="form-group">
                      <input type="password" name="password" id="password" tabindex="2" class="form-control"
                             placeholder="Password" v-model="formLogin.password">
                    </div>
                    <div class="form-group text-center">
                      <input type="checkbox" tabindex="3" class="" name="remember" id="remember">
                      <label for="remember"> Remember Me</label>
                    </div>
                    <div class="form-group">
                      <div class="row">
                        <div class="col-sm-6 col-sm-offset-3">
                          <button type="submit" name="login-submit" id="login-submit" tabindex="4"
                                  class="form-control btn btn-login" value="">Log In
                          </button>
                        </div>
                      </div>
                    </div>
                    <div class="form-group">
                      <div class="row">
                        <div class="col-lg-12">
                          <div class="text-center">
                            <a href="/register" tabindex="5" class="forgot-password">Register</a>
                          </div>
                        </div>
                      </div>
                    </div>
                  </form>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
import swal from 'sweetalert'

export default {
  name: 'login',
  data () {
    return {
      formLogin: {
        username: '',
        password: ''
      }
    }
  },
  methods: {
    login () {
      axios.post('http://localhost:3000/api/user/login', this.formLogin)
        .then((res) => {
          sessionStorage.setItem('id', res.data.id)
          sessionStorage.setItem('username', res.data.username)
          sessionStorage.setItem('name', res.data.name)

          // Redirect path simple chat
          window.location.href = '/simplechat'
        })
        .catch((error) => {
          console.log(error)
          swal('Fail !', error.response.data.messages + ' !', 'error')
        })
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="css">
@import "http://maxcdn.bootstrapcdn.com/bootstrap/3.3.0/css/bootstrap.min.css";
@import "../assets/css/login.css";
</style>
