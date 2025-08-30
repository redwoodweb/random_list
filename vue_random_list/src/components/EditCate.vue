<template>
  <div class="edit contents">
    <div v-if="loading">loading</div>

    <h2>Edit Category</h2>

    <div class="row">
      <div class="input-field col s8 m10">
        <input
          id="input_text"
          ref="inputText"
          v-model="inputText"
          type="text"
          @keyup.enter="inputTextFunc"
        />

        <label ref="labelText" for="input_text"></label>
      </div>

      <button class="btn large pink accent-3 col s4 m2" @click="inputTextFunc">
        <i class="fa fa-plus"></i>
      </button>
    </div>

    <div class="row">
      <form class="col s12">
        <div class="row">
          <div class="input-field col s12">
            <ul class="collection">
              <li class="collection-item">
                <div class="chip gray accent-2 black-text">{{ constCate }}</div>

                <div
                  v-for="items in cateList"
                  :key="items.id"
                  class="chip pink accent-2 white-text"
                >
                  {{ items }}
                  <i class="close-btn material-icons" @click="removeList(`${items}`)"> close </i>
                </div>
              </li>
            </ul>
          </div>
        </div>

        <button type="submit" class="btn pink accent-3 white-text" @click="updateEmployee">
          저장
        </button>

        <router-link
          :to="{ name: 'dashboard', params: { employee_id: user.ep_id } }"
          class="btn grey"
        >
          취소
        </router-link>
      </form>
    </div>
  </div>
</template>

<script>
import { db } from "./firebaseset"
export default {
  name: "Edit",
  data() {
    return {
      loading: false,
      user: {
        ep_id: null,
        list: null
      },
      inputText: "",
      currentCate: null,
      cateList: [],
      constCate: "mymenu"
    }
  },
  beforeRouteEnter(to, from, next) {
    db.collection("user")
      .where("ep_id", "==", to.params.employee_id)
      .get()
      .then((querySnapshop) => {
        querySnapshop.forEach((doc) => {
          next((vm) => {
            // vm.currentCate = to.query.current_cate
            vm.user.ep_id = doc.data().ep_id
            vm.user.list = doc.data().list
            let list = doc.data().list
            for (let key in list) {
              if (key !== vm.constCate) {
                console.log(key + "," + list[key])
                vm.cateList.push(key)
              }
            }
          })
        })
      })
  },
  watch: {
    $route: "fetchData"
  },
  mounted() {
    this.$nextTick(function () {
      window.localStorage.setItem("list", JSON.stringify(this.user.list))
      console.log(window.localStorage.getItem("list"))
      // widnow.localStorage.setItem('list',JSON.stringify(this.list)) // window.storage 저장
      // console.log(widnow.localStorage.get('list')+'local storage')
      // 모든 화면이 렌더링된 후 실행합니다.
      // let elem = this.$el
      // elem.querySelector('h2').style.margin = '0'
      // $(elem).find('h2').css('background', 'red')
    })
  },
  methods: {
    inputTextFunc: function () {
      if (this.inputText !== "") {
        this.cateList.push(this.inputText)
        this.user.list[this.inputText] = []
        console.log(this.user.list)
        window.localStorage.setItem("list", JSON.stringify(this.user.list)) // localStorage 추가
        console.log(window.localStorage.getItem("list"))
        db.collection("user")
          .where("ep_id", "==", this.user.ep_id)
          .get()
          .then((querySnapshop) => {
            querySnapshop.forEach((doc) => {
              doc.ref.update(this.user)
            })
          })
      }
      this.inputText = ""
      this.$refs.inputText.blur()
      this.$refs.labelText.classList.remove("active")
    },
    removeList: function (text) {
      // console.log(text)
      // for (const i in listelm) {
      //   // console.log(i)
      //   if (listelm[i] === text) {
      //     // console.log(listelm[i] + ',' + text)
      //     listelm.splice(i, 1)
      //   }
      // }
      for (const [key] of Object.entries(this.user.list)) {
        if (key === text) {
          delete this.user.list[key]
        }
      }
      let listelm = []
      console.log(this.cateList)
      for (const [key] of Object.entries(this.user.list)) {
        listelm.push(key)
      }
      this.cateList = listelm
      db.collection("user")
        .where("ep_id", "==", this.user.ep_id)
        .get()
        .then((querySnapshop) => {
          querySnapshop.forEach((doc) => {
            doc.ref.update(this.user)
          })
        })
    },
    updateEmployee() {
      db.collection("user")
        .where("ep_id", "==", this.$route.params.employee_id)
        .get()
        .then((querySnapshop) => {
          querySnapshop.forEach((doc) => {
            doc.ref
              .update(this.user)
              .then(() => {
                console.log("Document successfully update!")
                // this.$router.push({name: 'employeeview', params: {'employee_id': this.user.ep_id}})
                window.location.href = ""
              })
              .catch((error) => {
                console.error("Error removing document: ", error)
              })
          })
        })
    }
  }
}
</script>
