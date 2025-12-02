<template>
  <div class="login" style="max-width:420px;margin:3rem auto;padding:1.5rem;border:1px solid #ddd;border-radius:8px;">
    <h2 style="margin-bottom:0.5rem">Sign in</h2>

    <div style="margin-bottom:1rem">
      <label>Username</label>
      <input v-model="username" placeholder="choose a username" />
    </div>

    <div style="margin-bottom:1rem">
      <label>Password</label>
      <input v-model="password" type="password" placeholder="password" />
    </div>

    <div style="display:flex; gap:8px;">
      <button @click="signIn">Sign in</button>
      <button @click="createAccount">Create account</button>
    </div>

    <p style="color:red;margin-top:1rem" v-if="error">{{ error }}</p>

    <hr style="margin:1.5rem 0" />
    <details>
      <summary style="cursor:pointer">Demo accounts / notes</summary>
      <p style="font-size:0.9rem">Accounts are stored locally in your browser's localStorage. This is a simple demo — passwords are stored in plain text (not for production).</p>
    </details>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const username = ref('')
const password = ref('')
const error = ref('')

const emit = defineEmits(['login'])

function loadAccounts() {
  try {
    const raw = localStorage.getItem('accounts')
    return raw ? JSON.parse(raw) : {}
  } catch (e) {
    return {}
  }
}

function saveAccounts(accounts) {
  localStorage.setItem('accounts', JSON.stringify(accounts))
}

function signIn() {
  error.value = ''
  if (!username.value || !password.value) {
    error.value = 'Enter username and password.'
    return
  }
  const accounts = loadAccounts()
  const a = accounts[username.value]
  if (!a || a.password !== password.value) {
    error.value = 'Invalid username or password.'
    return
  }

  // Emit user object to parent
  const user = { username: username.value }
  emit('login', user)
}

function createAccount() {
  error.value = ''
  if (!username.value || !password.value) {
    error.value = 'Enter username and password to create an account.'
    return
  }
  const accounts = loadAccounts()
  if (accounts[username.value]) {
    error.value = 'Username already exists.'
    return
  }
  accounts[username.value] = { username: username.value, password: password.value }
  saveAccounts(accounts)

  // auto login
  emit('login', { username: username.value })

}
</script>
