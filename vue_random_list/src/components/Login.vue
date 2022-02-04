<template>
  <div class="login contents">
    <div>
      <div class="container">
        <div class="row">
          <div class="col s12 m8 offset-m2">
            <div class="login card-panel green white-text center">
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
                <button v-on:click="login" class="btn btn-large grey lighten-4 black-text">Login</button>
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
export default {
  name: 'register',
  data () {
    return {
      email: '',
      password: ''
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
            this.$router.go({ path: this.$router.path })
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
