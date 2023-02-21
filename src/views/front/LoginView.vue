<template>
  登入頁面
  <form id="form" class="form-signin" @submit.prevent="signin">
    <div class="form-floating mb-3">
      <!-- 綁定 -->
      <input v-model="userInfo.username" type="email" class="form-control" id="username"
        placeholder="name@example.com" required autofocus>
      <label for="username">Email address</label>
    </div>
    <div class="form-floating">
      <input v-model="userInfo.password" type="password" class="form-control" id="password"
        placeholder="Password" required>
      <label for="password">Password</label>
    </div>
    <button class="btn btn-lg btn-primary w-100 mt-3" type="submit">
      登入
    </button>
  </form>
</template>
<script>
const { VITE_APP_URL } = import.meta.env

export default {
  data () {
    return {
      userInfo: {
        username: '',
        password: ''
      }
    }
  },
  methods: {
    async signin () {
      if (this.userInfo.username !== '' && this.userInfo.password !== '') {
        try {
          const data = await this.$http.post(`${VITE_APP_URL}admin/signin`, this.userInfo)
          // 解構取出值
          const { token, expired } = data.data
          // 儲存資料         自訂義名稱
          document.cookie = `hexToken=${token}; expires=${new Date(expired)};`
          // location.href = './product.html'
          this.$router.push('/products')
        } catch (error) {
          console.log(error.response.data)
        }
        this.userInfo.username = ''
        this.userInfo.password = ''
      }
    }
  }

}
</script>
