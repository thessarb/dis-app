<template>
  <div id="app" class="app-root">
    <Login v-if="!user" @login="onLogin" />

    <div v-else class="app-shell">
      <aside :class="['sidebar', { open: sidebarOpen }]">
        <div class="sidebar-top">
          <div class="header-top">
            <h3>Categories</h3>
            <div>
              <button class="theme-toggle" @click="toggleTheme">{{ themeLabel }}</button>
            </div>
          </div>

          <div class="sidebar-list">
            <button @click="(activeComponent = null, sidebarOpen = false)">Home</button>
            <div style="height:8px"></div>
            <button v-for="c in componentNames" :key="c" @click="selectComponent(c)">{{ c }}</button>
          </div>
        </div>

        <div class="sidebar-footer">
          <div v-if="user" class="user-info">
            <div class="user-label">Signed in as</div>
            <div class="user-row">
              <strong class="user-name">{{ user.username }}</strong>
              <button class="logout-btn" @click="logout">Log out</button>
            </div>
          </div>
        </div>
  </aside>

  <!-- backdrop for mobile when sidebar is open -->
  <div v-if="sidebarOpen" class="sidebar-backdrop" @click="sidebarOpen = false"></div>

  <!-- mobile hamburger to open sidebar (bottom-left) -->
  <button class="hamburger" @click="sidebarOpen = true" aria-label="Open menu">☰</button>

      <main style="min-width:calc(100vw - 260px);">
        <div v-if="!activeComponent" class="home-container" style="display:flex;flex-direction:column;align-items:center;justify-content:center;height:calc(100vh - 2rem);text-align:center;">
          <h2 style="margin:0 0 1.25rem">Select an assessment category</h2>
          <div style="display:flex;flex-direction:column;gap:12px;align-items:center;width:100%">
            <button v-for="c in componentNames" :key="c" @click="selectComponent(c)" class="btn-accent" style="width:320px;max-width:90%;padding:14px 18px;font-size:1.05rem;border-radius:10px">{{ c }}</button>
          </div>
        </div>

        <div v-else>
          <div style="display:flex;align-items:center;justify-content:space-between;margin-bottom:0.5rem">
            <h2 style="margin:0">{{ activeComponent }}</h2>
            <div>
              <button @click="activeComponent = null">Back</button>
            </div>
          </div>

          <component :is="componentsMap[activeComponent]" />
        </div>
      </main>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, computed } from 'vue'
import Login from './components/Login.vue'
import Diabetes from './components/Diabetes.vue'
import ColdSeverity from './components/ColdSeverity.vue'
import HeartRisk from './components/HeartRisk.vue'
import Hypertension from './components/Hypertension.vue'

// map component display names to imported components
const componentsMap = {
  Diabetes,
  'Cold Severity Assessor': ColdSeverity,
  'Heart Attack Risk': HeartRisk,
  'Hypertension Risk': Hypertension,
}

const componentNames = Object.keys(componentsMap)

const user = ref(null)
const activeComponent = ref(null)

// sidebar open state for mobile overlay
const sidebarOpen = ref(false)

// theme can be 'light' or 'dark'
const theme = ref('dark')

const themeLabel = computed(() => theme.value === 'dark' ? '🌙 Dark' : '☀️ Light')

function loadUser() {
  try {
    const raw = localStorage.getItem('currentUser')
    if (raw) user.value = JSON.parse(raw)
  } catch (e) {
    user.value = null
  }
}

function loadTheme() {
  const t = localStorage.getItem('theme')
  if (t === 'light' || t === 'dark') theme.value = t
  applyTheme()
}

function applyTheme() {
  const root = document.documentElement
  root.classList.remove('theme-light','theme-dark')
  root.classList.add(theme.value === 'dark' ? 'theme-dark' : 'theme-light')
}

function toggleTheme() {
  theme.value = theme.value === 'dark' ? 'light' : 'dark'
  localStorage.setItem('theme', theme.value)
  applyTheme()
}

function onLogin(loggedUser) {
  user.value = loggedUser
  localStorage.setItem('currentUser', JSON.stringify(loggedUser))
}

function logout() {
  user.value = null
  localStorage.removeItem('currentUser')
  sidebarOpen.value = false
}

function selectComponent(name) {
  activeComponent.value = name
  // close mobile sidebar after selection
  sidebarOpen.value = false
}

onMounted(loadUser)
onMounted(loadTheme)
</script>
