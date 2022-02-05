<template>
  <div class="intro contents">
    <div class="input-field col s12">
      <input id="first_name" type="text" v-model="inputText" v-on:keyup.enter="inputTextFunc">
      <label for="first_name">First Name</label>
    </div>
    <div class="row">
      <div id="ramdomtext" v-bind:class="{'on': isActive}">{{ramdomText}}</div>
      <div class="row">
        <a v-on:click="suffleList" class="col s6 waves-effect waves-light btn center-text suffle-btn green"><i class="material-icons left">swap_vert</i>SUFFLE</a>
        <a v-on:click="resetList" class="col s6 waves-effect waves-light btn center-text reset-btn grey lighten-4 black-text"><i class="material-icons left">reply_all</i>RESET</a>
      </div>  
    </div>
    <ul class="collection">
      <!-- <li class="collection-header">
        <h4>food list</h4>
      </li> -->
      <li class="collection-item">
        <div v-for="items in user.list" v-bind:key="items.id" class="chip">{{items}}<i class="close-btn material-icons" v-on:click="removeList(`${items}`)">close</i></div>
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
      inputText: '',
      ramdomText: '텅',
      isActive: false   
    }
  },
  created () {    
    let currentUser = firebase.auth().currentUser.email
    db.collection('user').get().then(querySnapshop => {
      querySnapshop.forEach(doc => {
        if ( currentUser == doc.data().ep_id ) {
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
    inputTextFunc: function () {
      if ( this.inputText != '' ) {      
      this.user.list.push(this.inputText)
      db.collection('user').where( 'ep_id', '==' , this.user.ep_id ).get()
      .then(querySnapshop => {
        querySnapshop.forEach(doc => {
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
        if ( listelm[i] == text ) {
          console.log(listelm[i]+','+text)
          listelm.splice(i,1)
        }
      }
      db.collection('user').where( 'ep_id', '==' , this.user.ep_id ).get()
      .then(querySnapshop => {
        querySnapshop.forEach(doc => {
            doc.ref.update(this.user)
          })
        })
    },
    suffleList: function () {
      let listLength = this.user.list.length
      let ramdomNum = Math.floor(Math.random() * listLength)
      console.log(ramdomNum)
      this.ramdomText = this.user.list[ramdomNum]
      this.isActive = true
    },
    resetList: function () {
      this.ramdomText = '텅'
      this.isActive = false
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss" scoped>
  .collection {
    margin: 0.5rem 0;
    border: 0;
    .collection-item {
      background: none;
      border: 0;
      padding: 0;
      a.secondary-content {
        line-height: 38px;
      }
    }
  }
  .suffle-btn, .reset-btn {
    display: block;
    line-height: 5rem;
    height: 5rem;
    font-size: 1.5rem;
    i.material-icons {
      font-size: 1.7rem;
    }
  }
  #ramdomtext {
    font-size: 5rem;
    font-weight: 700;
    color: #c2c2c2;
    display: flex;
    justify-content: center;
    align-items: center;
    min-height: 15rem;
    &.on {
      color: #048d64;
    }
  }
</style>
