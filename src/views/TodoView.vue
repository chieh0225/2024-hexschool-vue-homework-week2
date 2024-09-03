<template>
  <div class="wrapper">
    <h2>註冊</h2>
    <form @submit.prevent="signUp">
      <input type="email" placeholder="Email" v-model="signupInfo.email" />
      <input type="password" placeholder="Password" v-model="signupInfo.password" />
      <input type="text" placeholder="Nickname" v-model="signupInfo.nickname" />

      <button @click="signUp">Sign Up</button>
    </form>
    <p>{{ messageSignUp }}</p>

    <h2>登入</h2>
    <form @submit.prevent="signIn">
      <input type="email" placeholder="Email" v-model="signinInfo.email" />
      <input type="password" placeholder="Password" v-model="signinInfo.password" />

      <button @click="signIn">Sign In</button>
      <p>Token: {{ token }}</p>
    </form>

    <h2>驗證</h2>
    <input placeholder="Token" v-model="tokenCheck" />
    <button @click="checkOut">Check Out</button>
    <p>{{ messageCheckOut }}</p>

    <h2>登出</h2>
    <input placeholder="Token" v-model="tokenSignOut" />
    <button @click="signOut">Sign Out</button>
    <p>{{ messageSignOut }}</p>
  </div>
</template>

<script setup>
import axios from 'axios'
import { ref } from 'vue'

const api = 'https://todolist-api.hexschool.io'

// 註冊
const messageSignUp = ref('')
const signupInfo = ref({
  email: '',
  password: '',
  nickname: ''
})

const signUp = async () => {
  try {
    const response = await axios.post(`${api}/users/sign_up`, signupInfo.value)
    messageSignUp.value = '註冊成功. UID: ' + response.data.uid
    console.log(response)
  } catch (error) {
    messageSignUp.value = '註冊失敗:' + error.message + ' ' + error.response.data.message
    console.log(error)
  }
}

// 登入
const token = ref('')
const signinInfo = ref({
  email: '',
  password: ''
})

const signIn = async () => {
  try {
    const response = await axios.post(`${api}/users/sign_in`, signinInfo.value)
    token.value = response.data.token
    document.cookie = `TodoToken=${response.data.token};`
    console.log(response)
  } catch (error) {
    token.value = '登入失敗: ' + error.message + ' ' + error.response.data.message
    console.log(error)
  }
}

// 驗證
const tokenCheck = ref('')
const messageCheckOut = ref('')

const checkOut = async () => {
  try {
    const response = await axios.get(`${api}/users/checkout`, {
      headers: {
        Authorization: tokenCheck.value
      }
    })
    messageCheckOut.value = '驗證成功 UID: ' + response.data.uid
    console.log(response)
  } catch (error) {
    messageCheckOut.value = '驗證失敗: ' + error.message + ' ' + error.response.data.message
    console.log(error)
  }
}

// 登出
const tokenSignOut = ref('')
const messageSignOut = ref('')

const signOut = async () => {
  try {
    const response = await axios.post(
      `${api}/users/sign_out`,
      {},
      {
        headers: {
          Authorization: tokenSignOut.value
        }
      }
    )
    messageSignOut.value = '登出成功.'
    console.log(response.data)
  } catch (error) {
    messageSignOut.value = '登出錯誤: ' + error.message
    console.log(error)
  }
}
</script>

<style>
.wrapper {
  max-width: 100%;
  box-sizing: border-box; /* 確保內邊距和邊框不會影響寬度 */
  margin-bottom: 32px;
}

h2 {
  font-weight: 700;
  margin-top: 32px;
  margin-bottom: 8px;
}

input {
  margin-bottom: 4px;
}

p {
  max-width: 500px; /* 或設定一個具體的最大寬度 */
  word-wrap: break-word; /* 自動換行 */
  overflow-wrap: break-word; /* 適用於較舊的瀏覽器 */
}
</style>
