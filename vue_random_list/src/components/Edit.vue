<template>
  <div class="edit contents">
    <div v-if="loading">loading</div>
    <h2>Edit</h2>
    <div class="row">
      <form v-on:submit.prevent="updateEmployee" class="col s12">
        <div class="row">
          <div class="input-field col s12">
            <input type="text" v-model="user.ep_id" id="user_id" required>
            <!-- <label for="user_id">employee id</label> -->
          </div>
        </div>
        <div class="row">
          <div class="input-field col s12">
            <input type="text" v-model="user.name" id="user_name" required>
            <!-- <label for="user_name">employee name</label> -->
          </div>
        </div>
        <div class="row">
          <div class="input-field col s12">
            <input type="text" v-model="user.age" id="user_age" required>
            <!-- <label for="user_age">employee age</label> -->
          </div>
        </div>
        <button type="submit" class="btn blue">저장</button>
        <router-link v-bind:to="{ name: 'employeeview', params: {employee_id: user.ep_id}}" class="btn grey">취소</router-link>
      </form>
    </div>
  </div>
</template>
<script>
import {db} from './firebaseset'
export default {
  name: 'edit',
  data () {
    return {
      loading: false,
      user: {
        ep_id: null,
        name: null,
        age: null
      }
    }
  },
  beforeRouteEnter (to, from, next) {
    db.collection('user').where('ep_id', '==', to.params.employee_id).get()
      .then(querySnapshop => {
        querySnapshop.forEach(doc => {
          next(vm => {
            vm.user.ep_id = doc.data().ep_id
            vm.user.name = doc.data().name
            vm.user.age = doc.data().age
            console.log(vm.user.ep_id = doc.data().ep_id)
          })
          // console.log(doc.data())
        })
      })
    // console.log(to.params.employee_id)
  },
  methods: {
    updateEmployee () {
      db.collection('user').where('ep_id', '==', this.$route.params.employee_id).get()
        .then(querySnapshop => {
          querySnapshop.forEach(doc => {
            doc.ref.update(this.user).then(() => {
              console.log('Document successfully update!')
              this.$router.push({name: 'employeeview', params: {'employee_id': this.user.ep_id}})
            }).catch((error) => {
              console.error('Error removing document: ', error)
            })
          })
        })
    }
  }
}
</script>
