<template>
  <div class="intro contents">
    <div class="row">
      <div class="input-field col s8 m10">
        <input id="input_text" type="text" v-model="inputText" v-on:keyup.enter="inputTextFunc">
        <label for="input_text">input your text</label>
      </div>
      <button class="btn large pink accent-3 col s4 m2" v-on:click="inputTextFunc"><i class="fa fa-plus"></i></button>
    </div>
    <ul class="collection">
      <li class="collection-item">
        <div v-for="items in user.list" v-bind:key="items.id" class="chip pink accent-2 white-text">{{items}}<i class="close-btn material-icons" v-on:click="removeList(`${items}`)">close</i></div>
      </li>
    </ul>
    <div class="row">
      <div id="ramdomtext" v-bind:class="{'on': isActive}">{{ramdomText}}</div>
      <div class="row btn-group">
        <div class="col s6">
          <a v-on:click="suffleList" class=" waves-effect waves-light btn center-text suffle-btn pink accent-3">SUFFLE</a>
        </div>
        <div class="col s6">
          <a v-on:click="resetList" class="waves-effect waves-light btn center-text reset-btn grey darken-1 white-text">RESET</a>
        </div>              
      </div>  
    </div>
    <!-- <div class="fixed-action-btn">
      <button class="btn-floating btn-large red" v-on:click="inputTextFunc"><i class="fa fa-plus"></i></button>
    </div> -->
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
      this.$el.querySellector('#nput_text + label').classList.removeClass = 'active'
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
  .btn-group {
    position: fixed;
    height: 5rem;
    bottom: 0;
    left: 0;
    width: 100%;
    z-index: 555;
    margin-bottom: 0;
    background: rgba( 252, 228, 236, .8);
    @media screen and ( min-width: 601px ) {      
      width: 80%;
      left: 50%;
      transform: translateX( -50% );
    }
    @media screen and ( min-width: 993px ) {
      width: 70%;
      left: 50%;
      transform: translateX( -50% );
    }  
  }
  .suffle-btn, .reset-btn {
    display: block;
    line-height: 5rem;
    height: 5rem;
    font-size: 1.5rem;
    border-radius: 2.5rem;
    i.material-icons {
      font-size: 1.7rem;
    }
  }
  .reset-btn {
    color: #999999;    
  }
  .input-field {
    padding: 0;
    & > label:not(.label-icon).active {
      -webkit-transform: translate(0px, 0px) scale(0.4);
      transform: translate(0px, 0px) scale(0.4);
      -webkit-transform-origin: 0 0;
      transform-origin: 0 0;
    }  
    input,label {
      height: 5rem;
      font-size: 2rem;
    }
    label {
      left: 2rem;
    }
    input {
      background: white;
      display: inline-block;
      box-sizing: border-box;
      padding: 0 2rem;
      border: 1px solid #9e9e9e;
      border-right: 0px;
      border-radius: 1rem 0 0 1rem;      
    }
    & + button {
      margin: 1rem 0;
      height: 5rem;
      font-size: 2rem;
      border-radius: 0 1rem 1rem 0;
      i {
        font-size: 2rem;
      }
    }
  }
  #ramdomtext {
    font-size: 5rem;
    font-weight: 700;
    color: white;
    display: flex;
    justify-content: center;
    align-items: center;
    min-height: 15rem;
    &.on {
      color: #ff4081;;
    }
  }
</style>
