<template>
  <div class="intro contents">
    <div class="fixed-action-btn">
      <router-link v-bind:to="{ name: 'editcate', params: {employee_id: user.ep_id}}" class="btn-floating btn-large red"><i class="fa fa-pencil"></i></router-link>
    </div>
    <div class="random-box-wrap" v-bind:class="{'on': isVisible}">
      <div class="inner">
        <div ref="randombox" class="randombox">
          <div v-for="(items, i) in mlist" v-bind:key="i">{{items}}</div>
        </div>
      </div>
    </div>
    <div class="row">
      <div class="dropdown" v-on:click="dropDown()">
        <div class="content">
          <input type="text" placeholder="" disabled  v-bind:value='currentCate'>
          <div class="inner">
            <div v-for="li in cate" v-bind:key="li" class="option" v-on:click="show(li)">{{li}}</div>
          </div>
        </div>
      </div>
    </div>
    <div class="row">
      <div class="input-field col s8 m10">
        <input ref="inputText" id="input_text" type="text" v-model="inputText" v-on:keyup.enter="inputTextFunc">
        <label ref="labelText" for="input_text">input your text</label>
      </div>
      <button class="btn large pink accent-3 col s4 m2" v-on:click="inputTextFunc"><i class="fa fa-plus"></i></button>
    </div>
    <ul class="collection">
      <li class="collection-item">
        <div v-for="items in mlist" v-bind:key="items.id" class="chip pink accent-2 white-text">{{items}}<i class="close-btn material-icons" v-on:click="removeList(`${items}`)">close</i></div>
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
      user: {
        ep_id: null,
        list: []
      },
      currentCate: 'mymenu',
      cate: [],
      inputText: '',
      ramdomText: '텅',
      isActive: false,
      selectElem: null,
      mlist: [],
      isVisible: false,
      timeOutId: null,
      count: 0
    }
  },
  created () {
    let currentUser = firebase.auth().currentUser.email
    db.collection('user').get().then(querySnapshop => {
      querySnapshop.forEach(doc => {
        if (currentUser === doc.data().ep_id) {
          this.user.ep_id = doc.data().ep_id
          this.user.list = doc.data().list
          // console.log(doc.data().list)
          // 카테고리 목록 가져오기
          // for( const items in doc.data().list){
          //   console.log(items)
          // }
          for (const [key] of Object.entries(doc.data().list)) {
            this.cate.push(key)
            // console.log("카테고리모음"+this.cate)
          }
          // 현재 카테고리에 할당
          // this.currentCate = this.cate[0]
          // console.log(this.user.list)
          // 현재 카테고리에 매칭된 데이터 가져오기
          for (const [key, value] of Object.entries(doc.data().list)) {
            if (key === this.currentCate) {
              value.forEach(val => {
                this.mlist.push(val)
              })
            }
          }
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
      if (this.inputText !== '') {
        this.mlist.push(this.inputText)
        for (const [key, value] of Object.entries(this.user.list)) {
          if (key === this.currentCate) {
            value.push(this.inputText)
          }
        }
        db.collection('user').where('ep_id', '==', this.user.ep_id).get()
          .then(querySnapshop => {
            querySnapshop.forEach(doc => {
              doc.ref.update(this.user)
            })
          })
      }
      this.inputText = ''
      this.$refs.inputText.blur()
      this.$refs.labelText.classList.remove('active')
    },
    removeList: function (text) {
      // console.log(text)
      let listelm = this.user.list
      for (const i in listelm) {
        // console.log(i)
        if (listelm[i] === text) {
          // console.log(listelm[i] + ',' + text)
          listelm.splice(i, 1)
        }
      }
      for (const [key, value] of Object.entries(this.user.list)) {
        if (key === this.currentCate) {
          for (const i in value) {
            if (value[i] === text) {
              value.splice(i, 1)
              this.mlist = value
            }
          }
        }
      }
      db.collection('user').where('ep_id', '==', this.user.ep_id).get()
        .then(querySnapshop => {
          querySnapshop.forEach(doc => {
            doc.ref.update(this.user)
          })
        })
    },
    aniFunc: function () {
      let addNum = 20
      this.timeOutId = window.setTimeout(() => {
        if (this.mlist.length === this.count) {
          this.count = 0
        }
        addNum += this.count * 20 * (-1)// 첫번째 메뉴일때 하단에서 올라오는 효과 주기  settimeout이 종료될때 항상 count 0으로 종료되기때문
        this.$refs.randombox.style.transform = 'translateY(' + addNum + 'vh)'
        this.count += 1 //  count 1추가 후 종료 cleartimeout 이후 아래코드에서 count 출력 시 기존 현재 카운터 보다 1 많음
        this.aniFunc()
      }, 80)
    },
    suffleList: function () {
      let listLength = this.mlist.length
      let randomNum = Math.floor(Math.random() * listLength)
      // console.log("index:"+randomNum)
      this.isActive = true
      this.isVisible = true
      this.aniFunc()
      window.setTimeout(() => {
        clearTimeout(this.timeOutId)
        this.$refs.randombox.classList.add('transition')
        // console.log(this.$refs.randombox.style.transform)
        this.$refs.randombox.style.transform = 'translateY(' + randomNum * 20 * (-1) + 'vh)'
      }, 800)
      window.setTimeout(() => {
        this.isVisible = false
        this.count = 0
        this.$refs.randombox.classList.remove('transition')
        this.$refs.randombox.style.transform = 'translateY(0vh)'
        this.ramdomText = this.mlist[randomNum]
      }, 2500)
    },
    resetList: function () {
      this.ramdomText = '텅'
      this.isActive = false
    },
    show: function (elem) {
      document.querySelector('input').value = elem
      let currentUser = firebase.auth().currentUser.email
      db.collection('user').get().then(querySnapshop => {
        querySnapshop.forEach(doc => {
          if (currentUser === doc.data().ep_id) {
            this.user.ep_id = doc.data().ep_id
            this.user.list = doc.data().list
            // console.log(doc.data().list)
            // 현재 카테고리에 할당
            this.currentCate = elem
            // console.log(this.user.list)
            // 현재 카테고리에 매칭된 데이터 가져오기
            for (const [key, value] of Object.entries(doc.data().list)) {
              if (key === this.currentCate) {
                this.mlist = value
              }
            }
          }
        })
        this.ramdomText = '텅'
        this.isActive = false
      })
    },
    dropDown: function () {
      document.querySelector('.dropdown').classList.toggle('active')
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
      font-size: 2rem;
    }
    label {
      left: 2rem;
    }
    input {
      height: 5rem;
      background: white;
      display: inline-block;
      box-sizing: border-box;
      padding: 0 2rem;
      border: 1px solid #9e9e9e;
      border-right: 0px;
      border-bottom-style: inset;
      border-radius: 1rem 0 0 1rem;
      }
      & + button.btn {
      box-shadow: none;
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
    min-height: 10rem;
    text-align: center;
    &.on {
      color: #ff4081;
      }
  }
  input:not([type]):focus:not([readonly]), input[type=text]:not(.browser-default):focus:not([readonly]), input[type=password]:not(.browser-default):focus:not([readonly]), input[type=email]:not(.browser-default):focus:not([readonly]), input[type=url]:not(.browser-default):focus:not([readonly]), input[type=time]:not(.browser-default):focus:not([readonly]), input[type=date]:not(.browser-default):focus:not([readonly]), input[type=datetime]:not(.browser-default):focus:not([readonly]), input[type=datetime-local]:not(.browser-default):focus:not([readonly]), input[type=tel]:not(.browser-default):focus:not([readonly]), input[type=number]:not(.browser-default):focus:not([readonly]), input[type=search]:not(.browser-default):focus:not([readonly]), textarea.materialize-textarea:focus:not([readonly]) {
    border: 1px solid #f50057;
    border-right: 0;
    -webkit-box-shadow: 0 0px 0 0 #f50057;
    box-shadow: 0 0px 0 0 #f50057;
  }
</style>
