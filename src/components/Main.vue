<template>
  <div>
    <h2 v-show="firstView">请输入关键字搜索</h2>
    <h2 v-show="loading">请求中...</h2>
    <h2 v-show="errorMsg">{{errorMsg}}</h2>
    <div class="row" v-show="users.length>0">
      <div class="card" v-for="(user, index) in users" :key="index">
        <a :href="user.url" target="_blank">
          <img :src="user.avatarUrl" style='width: 100px'/>
        </a>
        <p class="card-text">{{user.username}}</p>
      </div>
    </div>
  </div>
</template>

<script>
  import PubSub from 'pubsub-js'
  import axios from 'axios'
  export default {
    data () {
      return {
        firstView: true, 
        loading: false, 
        users: [], 
        errorMsg: '' 
      }
    },

    mounted () {
      PubSub.subscribe('search', (message, searchName) => { 
        this.firstView = false
        this.loading = true
        this.users = []
        this.errorMsg = ''

        const url = `https://api.github.com/search/users?q=${searchName}`
        axios.get(url)
          .then(response => {
            this.loading = false
            this.users = response.data.items.map(item => ({
              url: item.html_url,
              avatarUrl: item.avatar_url,
              username: item.login
            }))
          })
          .catch(error => {
            this.loading = false
            this.errorMsg = '请求失败!'
          })




      })
    }
  }
</script>

<style>
  .card {
    float: left;
    width: 33.333%;
    padding: .75rem;
    margin-bottom: 2rem;
    border: 1px solid #efefef;
    text-align: center;
  }

  .card > img {
    margin-bottom: .75rem;
    border-radius: 100px;
  }

  .card-text {
    font-size: 85%;
  }
</style>