<template>
  <div class="container column" >
    <form class="card card-w30" @submit.prevent="createBlock">
      <div class="form-control">
        <label for="type">Тип блока</label>
        <select id="type" v-model="block">
          <option value="title">Заголовок</option>
          <option value="subtitle">Подзаголовок</option>
          <option value="avatar">Аватар</option>
          <option value="text">Текст</option>
        </select>
      </div>

      <app-input
        label="Значение"
        :id="this.block"
        v-model.trim="blockdata"
      ></app-input>

      <button
              class="btn primary"
              :disabled="blockdata.length <= 3"
              @keyup.enter.exact="createBlock($event)"

      >Добавить</button>
    </form>

    <div class="card card-w70" v-cloak>
      <app-title
              :title="this.title"
      ></app-title>
      <app-avatar
              :avatar="this.avatar"
      ></app-avatar>
      <app-subtitle
              v-for="(item, idx) in this.subtitle"
              :key="idx"
              :subtitle="item"
              :text="this.text[idx]"></app-subtitle>
      <h3 v-if="noblock"
      >Добавьте первый блок, чтобы увидеть результат</h3>
    </div>
  </div>
  <app-loader v-if="loading"></app-loader>
  <app-comments
          v-else
          :comments="comments"
          label="Комментарии"
          @load="loadComments"
  ></app-comments>
</template>

<script>
  import AppInput from "./AppInput";
  import AppTitle from "./AppTitle";
  import AppSubtitle from "./AppSubtitle";
  import AppAvatar from "./AppAvatar";
  import AppLoader from "./AppLoader";
  import AppComments from "./AppComments";
  import axios from 'axios'

  export default {
    data() {
      return {
        blockdata: '',
        block: 'title',
        title: '',
        subtitle: [],
        avatar: '',
        text: [],
        resume: [],
        loading: false,
        noblock: false,
        comments: []
      }
    },
    mounted() {
      this.loadBlock()
    },
    methods: {
      async createBlock() {
        const response = await fetch('https://vue-resume-52c6b-default-rtdb.firebaseio.com/resume.json', {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json'
          },
          body: JSON.stringify({
            block: this.block,
            blockdata: this.blockdata
          })
        })

        const firebaseData = await response.json()

        this.resume.push({
          block: this.block,
          blockdata: this.blockdata,
          id: firebaseData.name
        })
        this.blockdata = ''
        this.loadBlock()
      },
      async loadBlock() {
        const {data} = await axios.get('https://vue-resume-52c6b-default-rtdb.firebaseio.com/resume.json')
        try {
          this.resume = Object.keys(data).map(key => {
            return {
              id: key,
              block: data[key].block,
              blockdata: data[key].blockdata
            }
          })
        } catch (e) {
          this.checkBlock()
        }
        this.getdata()
      },
  getdata() {
        this.subtitle = []
        this.text = []
        for (var i = 0; i < this.resume.length; i++ ) {
          console.log(this.resume[i])
          if (this.resume[i]['block'] === 'title') {
            this.title = this.resume[i]['blockdata']
          }
          if (this.resume[i].block === 'subtitle') {
            this.subtitle.push(this.resume[i].blockdata)
            console.log('субтитл', this.subtitle)
          }
          if (this.resume[i].block === 'avatar') {
            this.avatar = this.resume[i].blockdata
          }
          if (this.resume[i].block === 'text') {
            this.text.push(this.resume[i].blockdata)
          }
        }
        this.checkBlock()
      },
      checkBlock() {
        if (this.resume.length > 0) {
          console.log('if', false)
          return this.noblock = false
        } else {
          console.log('else',true)
          return this.noblock = true
        }

      },
      async loadComments() {
        this.loading = true
        const {data} = await axios.get('https://jsonplaceholder.typicode.com/comments?_limit=42')
        this.comments = Object.keys(data).map(key => {
          return {
            id: key,
            email: data[key].email,
            body: data[key].body
          }
        })
        this.loading = false
      }
    },
    components: {AppInput, AppTitle, AppSubtitle, AppAvatar, AppLoader, AppComments}

  }
</script>

<style>
  .avatar {
    display: flex;
    justify-content: center;
  }

  .avatar img {
    width: 150px;
    height: auto;
    border-radius: 50%;
  }
</style>
