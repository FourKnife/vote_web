<template>
  <div class="page-wrapper">
    <div class="login-box">
      <div class="row">
        <h1 class="login-title">账户登录</h1>
      </div>
      <div class="row">
        <input type="text" id="username" class="input" v-model="loginForm.username" placeholder="用户名">
      </div>
      <div class="row">
        <input type="password" id="password" class="input" v-model="loginForm.password" placeholder="密码">
      </div>
      <input type="submit" class="submit" value="登  录" @click="handleOnClickLogin">
    </div>
    <Spin size="large" fix v-if="spin"></Spin>
  </div>
</template>

<script>
  import axios from 'axios'

  export default {
    name: 'login',
    data() {
      return {
        loginForm: {
          username: '',
          password: ''
        },
        spin: false
      }
    },
    mounted() {
      if (this.$route.query.hasOwnProperty('redirect')) {
        this.$Notice.warning({title:"请先登录!"})
      }
    },
    methods: {
      handleOnClickLogin() {
        this.spin = true
        axios({
          url: `${apiPath}/login`,
          method: 'post',
          data: {
            uid: this.loginForm.username,
            password: this.loginForm.password
          },
          timeout: 10000
        }).then((res) => {
          this.spin = false
          if (res.data.code === "SUCCESS") {
            localStorage.setItem('token', res.data.data.token)
            localStorage.setItem('identity', res.data.data.identity)
            console.log(res.data.data)
            localStorage.setItem('partyMaxVoteNum', res.data.data.voteMaxNum.partMaxVoteNum)
            localStorage.setItem('groupMaxVoteNum', res.data.data.voteMaxNum.groupMaxVoteNum)
            //是否有重定向参数
            if (this.$route.query.hasOwnProperty('redirect'))
              this.$router.push({path: this.$route.query.redirect})
            else
              this.$router.push({name: "selectBranch"})
          } else {
            this.$Notice.warning({title:"请检查工号或密码是否正确"})
          }
        }).catch((err) => {
          this.spin = false
          this.$Notice.warning({title:`发生错误：${err}`})
        })
      }
    }
  }
</script>

<style lang="scss" scoped>
  @import '../../common/scss/login';
</style>
