<template>
  <div class="intro contents">
    <h2>Dashboard</h2>
    <ul class="collection with-header">
      <li class="collection-header">
        <h4>employee</h4>
      </li>
      <li v-for="items in employee" v-bind:key="items.id" class="collection-item">
        <div class="chip">{{items.ep_id}}</div>{{items.name}}
        <router-link v-bind:to="{ name: 'employeeview', params: {employee_id: items.ep_id} }" class="secondary-content">
          <i class="fa fa-eye"></i>
        </router-link>
      </li>
    </ul>
    <div class="fixed-action-btn">
      <router-link :to="{ name: 'new' }" class="btn-floating btn-large red"><i class="fa fa-plus"></i></router-link>
    </div>
  </div>
</template>
<script>
import {db} from './firebaseset'
export default {
  name: 'DashBoard',
  data () {
    return {
      employee: []
    }
  },
  created () {
    db.collection('user').get().then(querySnapshop => {
      querySnapshop.forEach(doc => {
        console.log(doc.data().age)
        const data = {
          'id': doc.id,
          'ep_id': doc.data().ep_id,
          'name': doc.data().name,
          'age': doc.data().age
        }
        this.employee.push(data)
      })
    })
  },
  mounted () {
    this.$nextTick(function () {
      // 모든 화면이 렌더링된 후 실행합니다.
      // let elem = this.$el
      // elem.querySelector('h2').style.margin = '0'
      // $(elem).find('h2').css('background', 'red')
    })
  }
  // mounted () {
  //   db.collection('user').get().then((querySnapshot) => {
  //     querySnapshot.forEach((doc, i) => {
  //       console.log(doc.data().age)
  //       this.userId.push(doc.data().name)
  //       // console.log(doc.data().name)
  //     })
  //   })
  // }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss" scoped>
  .collection-item {
    a.secondary-content {
      line-height: 38px;
    }
  }
</style>
