<template>
  <nav>
    <div id="nav" class="nav-wrapper green">
      <div class="container">
        <router-link to="/" class="brand-logo">rihnoboard</router-link>
        <ul class="right">
          <li v-if="isLoggedIn"><router-link to="/">Dashboard</router-link></li>
          <li v-if="!isLoggedIn"><router-link to="/login">Login</router-link></li>
          <li v-if="!isLoggedIn"><router-link to="/register">Resiter</router-link></li>
          <li v-if="isLoggedIn" v-on:click="logout">Logout</li>
        </ul>
      </div>
      <!-- <ul class="row">
        <li v-for="(menu,i) in gnb " v-bind:key="`${menu.name}-${i}`" v-bind:class="'col s2'">
          <router-link v-bind:to="{ name: menu.name }" class="white-text">{{menu.name}}</router-link>
        </li>
      </ul> -->
    </div>
  </nav>
</template>

<script>
import firebase from 'firebase'
export default {
  name: 'navbar',
  data () {
    return {
      isLoggedIn: false,
      currentUser: false
    }
  },
  created () {
    console.log(firebase.auth().currentUser)
    if (firebase.auth().currentUser) {
      this.isLoggedIn = true
      console.log(this.isLoggedIn)
      this.currentUser = firebase.auth().currentUser.email
    }
  },
  methods: {
    logout: function () {
      console.log('logout')
      firebase.auth().signOut().then(() => {
        console.log('logout')
        // this.$router.push('login')
        this.$router.go({ path: this.$router.path })
      })
    }
  }
}
</script>

<style lang="scss" scoped>
  .nav-wrapper{
    ul{
      li{
        a{
          display: inline-block;
        }
      }
    }
  }
</style>
