<template>
  <div class="login contents">
    <div>
      <div class="container">
        <div class="row">
          <div class="col s12 m8 offset-m2">
            <div class="login card-panel grey lighten-4 black-text center">
              <h3>Register</h3>
              <form>
                <div class="input-field">
                  <i class="material-icons prefix">email</i>
                  <input type="text" id="email" name="" v-model="email">
                  <label class="black-text" for="email">Email</label>
                </div>
                <div class="input-field">
                  <i class="material-icons prefix">lock</i>
                  <input type="password" id="password" name="" v-model="password">
                  <label class="black-text" for="password">Password</label>
                </div>
                <button v-on:click="register" class="btn btn-large grey lighten-4 grey-text">Register</button>
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
  name: 'login',
  data () {
    return {
      email: '',
      password: '',
      user: {
        ep_id: null,
        name: null,
        age: null
      }
    }
  },
  methods: {
    register: function (e) {
      firebase.auth()
        .createUserWithEmailAndPassword(this.email.trim(), this.password)
        .then(user => {
          alert(`Acount created for ${this.email}`)
          this.$router.push('/')
          this.user.ep_id = this.email.trim()
          // db.collection('user').add(this.user) /*가입 후 해당 아이디 관련 데이터 생성 */
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
</style>
