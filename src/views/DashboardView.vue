<template>
  這是專屬後台的頁面
    <router-link to="/back/products">後台產品列表</router-link> |
    <router-link to="/back/orders">後台訂單列表</router-link> |
    <router-link to="/">回前台首頁</router-link> |
    <a href="#" @click.prevent="logout">登出</a> |
    <hr>
    <RouterView></RouterView>
</template>

<script>
import { RouterView } from 'vue-router'
const { VITE_APP_URL } = import.meta.env

export default {
  components: {
    RouterView
  },
  methods: {
    logout () {
      document.cookie = `hexToken=; expires=${new Date()};`
      this.$router.push('/login')
    },
    // async checkSignin () {
    //   //  取得 Token（Token 僅需要設定一次）
    //   //                                                   前面自定義名稱
    //   const token = document.cookie.replace(/(?:(?:^|.*;\s*)hexToken\s*=\s*([^;]*).*$)|^.*$/, '$1')
    //   // axios 登入抓住 token
    //   this.$http.defaults.headers.common.Authorization = token
    //   try {
    //     await this.$http.post(`${VITE_APP_URL}api/user/check`)
    //   } catch (error) { // 抓錯誤
    //     this.$router.push('/login')
    //     // console.log(error.data.message)
    //   }
    // }
    checkSignin () {
      const token = document.cookie.replace(/(?:(?:^|.*;\s*)hexToken\s*=\s*([^;]*).*$)|^.*$/, '$1')
      // axios 登入抓住 token
      this.$http.defaults.headers.common.Authorization = token
      this.$http.post(`${VITE_APP_URL}api/user/check`)
        .then((res) => {
          if (!res.data.success) {
            this.$router.push('/login')
          }
        })
    }
  },
  mounted () {
    this.checkSignin()
  }
}
</script>
