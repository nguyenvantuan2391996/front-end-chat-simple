<template>
  <div>
    <div class="container">
      <div class="row">
        <div class="col-md-6 col-md-offset-3">
          <div class="panel panel-login">
            <div class="panel-heading">
              <div class="row">
                <div class="col-xs-12">
                  <h3>Register</h3>
                </div>
              </div>
              <hr>
            </div>
            <div class="panel-body">
              <div class="row">
                <div class="col-lg-12">

                  <form @submit.prevent="registerAccount" method="post">
                    <div class="form-group">
                      <input type="text" name="username" tabindex="1" class="form-control" placeholder="Username"
                             v-model="formRegister.username" required>
                    </div>
                    <div class="form-group">
                      <input type="text" name="name" tabindex="1" class="form-control" placeholder="Name"
                             v-model="formRegister.name" required>
                    </div>
                    <div class="form-group">
                      <input type="password" name="password" tabindex="2" class="form-control" placeholder="Password"
                             v-model="formRegister.password" required>
                    </div>
                    <div class="form-group">
                      <div class="row">
                        <div class="col-sm-6 col-sm-offset-3">
                          <button type="submit" name="register-submit" id="register-submit" tabindex="4"
                                  class="form-control btn btn-register">Register Now
                          </button>
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
  name: 'register',
  data () {
    return {
      formRegister: {
        username: '',
        name: '',
        password: ''
      }
    }
  },
  methods: {
    registerAccount () {
      axios.post('http://localhost:3000/api/user', this.formRegister)
        .then((res) => {
          swal('Account is registered successfully !', {
            icon: 'success',
            timer: 500
          })

          // wait 3s to redirect path login
          setTimeout(function () {
            window.location.href = '/'
          }, 1000)
        })
        .catch((error) => {
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
