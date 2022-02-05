<template>
  <div class="login contents">
    <div>
      <div class="container">
        <div class="row">
          <div class="col s12 m10 offset-m1">
            <div class="login card-panel gray lighten-3 black-text center">
              <h3>Login</h3>
              <form>
                <div class="input-field">
                  <i class="material-icons prefix">email</i>
                  <input type="email" id="email" name="" value="" v-model="email">
                  <label class="white-text" for="email">Email</label>
                </div>
                <div class="input-field">
                  <i class="material-icons prefix">lock</i>
                  <input type="password" id="password" name="" value="" v-model="password">
                  <label class="white-text" for="password">Password</label>
                </div>
                <button v-on:click="login" class="btn btn-large green white-text">Login</button>
              </form>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import firebase from 'firebase'
import {db} from './firebaseset'
export default {
  name: 'register',
  data () {
    return {
      email: '',
      password: '',
      user: {
        id: '',
        list: ''
      }
    }
  },
  methods: {
    login: function (e) {
      // console.log(this.email)
      firebase
        .auth()
        .signInWithEmailAndPassword(this.email.trim(), this.password)
        .then(
          user => {
            alert(`You logined in as ${this.email}`)
            // console.log(this.user)
            // this.$router.push('/')
            // this.$router.go()            
            window.location.href = ''
          },
          err => {
            alert(err.message)
          }
        )

      e.preventDefault()
    }
  }
}
</script>

<style lang="css" scoped>
.btn {
  display: block;
  width: 100%;
}
</style>
