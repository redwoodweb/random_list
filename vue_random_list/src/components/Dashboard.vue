<template>
  <div class="intro contents">
    <div class="input-field col s12">
      <input id="first_name" type="text" v-model="inputText" v-on:keyup.enter="inputTextFunc">
      <label for="first_name">First Name</label>
    </div>
    <div>{{inputText}}</div>
    <ul class="collection with-header">
      <li class="collection-header">
        <h4>food list</h4>
      </li>
      <li v-for="(items,i) in user.list" v-bind:key="items.id" class="collection-item">
        <div class="chip">{{i}}{{items}}<i class="close material-icons" v-on:click="removeList(`${items}`)">close</i></div>
      </li>
    </ul>
    <div class="fixed-action-btn">
      <button class="btn-floating btn-large red" v-on:click="inputTextFunc"><i class="fa fa-plus"></i></button>
    </div>
  </div>
</template>
<script>
import firebase from 'firebase'
import {db} from './firebaseset'
export default {
  name: 'DashBoard',
  data () {
    return {
      user:{
        ep_id: null,
        list: []
      },
      inputText: ''      
    }
  },
  created () {    
    let currentUser = firebase.auth().currentUser.email    
    db.collection('user').get().then(querySnapshop => {
      querySnapshop.forEach(doc => {
        if( currentUser == doc.data().ep_id ) {
          this.user.ep_id = doc.data().ep_id
          this.user.list = doc.data().list
        }
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
  },
  methods: {
    inputTextFunc: function() {           
      if( this.inputText != '' ) {
      this.user.list.push(this.inputText)
      db.collection('user').where( 'ep_id', '==' , this.user.ep_id ).get()
      .then( querySnapshop => {
        querySnapshop.forEach( doc => {
          doc.ref.update(this.user)
        })
      })
      }
      this.inputText = ''
    },
    removeList: function (text) {
      console.log(text)
      let listelm = this.user.list
      for ( const i in listelm) {
        console.log(i)
        if( listelm[i] == text ) {
          console.log(listelm[i]+','+text)
          listelm.splice(i,1)
        }
      }
      console.log(listelm) 
    }
  }
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
