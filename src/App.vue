<script setup>
import { ref } from 'vue'
import RatioConverter from './components/RatioConverter.vue'
import PixelToEm from './components/PixelToEm.vue'
import LineHeightCalculator from './components/LineHeightCalculator.vue'

const currentTool = ref('ratio')
const menuOpen = ref(false)

const tools = [
  { id: 'ratio', name: 'Ratio Converter', component: RatioConverter },
  { id: 'pixel-em', name: 'Pixel to Em', component: PixelToEm },
  { id: 'line-height', name: 'Line Height Calculator', component: LineHeightCalculator }
]

const toggleMenu = () => {
  menuOpen.value = !menuOpen.value
}

const selectTool = (toolId) => {
  currentTool.value = toolId
  menuOpen.value = false
}
</script>

<template>
  <div class="app">
    <!-- Mobile menu button -->
    <button class="menu-toggle" @click="toggleMenu" aria-label="Toggle menu">
      <span></span>
      <span></span>
      <span></span>
    </button>

    <!-- Sidebar -->
    <aside class="sidebar" :class="{ 'sidebar--open': menuOpen }">
      <div class="sidebar__header">
        <h1 class="sidebar__title">Web Dev Tools</h1>
      </div>
      <nav class="sidebar__nav">
        <ul class="nav-list">
          <li v-for="tool in tools" :key="tool.id" class="nav-item">
            <button 
              @click="selectTool(tool.id)"
              :class="['nav-button', { 'nav-button--active': currentTool === tool.id }]"
            >
              {{ tool.name }}
            </button>
          </li>
        </ul>
      </nav>
    </aside>

    <!-- Main content -->
    <main class="main" :class="{ 'main--menu-open': menuOpen }">
      <div class="tool-container">
        <component :is="tools.find(t => t.id === currentTool)?.component" />
      </div>
    </main>

    <!-- Overlay for mobile -->
    <div v-if="menuOpen" class="overlay" @click="toggleMenu"></div>
  </div>
</template>

<style>
:root {
  --color-primary: #003049;
  --color-secondary: #d62828;
  --color-accent: #f77f00;
  --color-accent-light: #fcbf49;
  --color-background: #eae2b7;
  --color-white: #ffffff;
  --color-text: #2c3e50;
  --color-text-light: #6c757d;
  --sidebar-width: 280px;
}

* {
  box-sizing: border-box;
}

body {
  margin: 0;
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, sans-serif;
  background-color: var(--color-background);
  color: var(--color-text);
  line-height: 1.6;
}

.app {
  display: flex;
  min-height: 100vh;
  position: relative;
}

.menu-toggle {
  display: none;
  position: fixed;
  top: 1rem;
  left: 1rem;
  z-index: 1001;
  background: var(--color-primary);
  border: none;
  border-radius: 8px;
  width: 44px;
  height: 44px;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  gap: 4px;
  cursor: pointer;
  transition: all 0.3s ease;
}

.menu-toggle span {
  width: 20px;
  height: 2px;
  background: var(--color-white);
  border-radius: 1px;
  transition: all 0.3s ease;
}

.menu-toggle:hover {
  background: var(--color-secondary);
}

.sidebar {
  width: var(--sidebar-width);
  background: var(--color-primary);
  color: var(--color-white);
  padding: 2rem 0;
  position: fixed;
  left: 0;
  top: 0;
  height: 100vh;
  overflow-y: auto;
  transition: transform 0.3s ease;
  z-index: 1000;
}

.sidebar__header {
  padding: 0 2rem;
  margin-bottom: 2rem;
}

.sidebar__title {
  margin: 0;
  font-size: 1.5rem;
  font-weight: 600;
  color: var(--color-accent-light);
}

.nav-list {
  list-style: none;
  padding: 0;
  margin: 0;
}

.nav-item {
  margin-bottom: 0.5rem;
}

.nav-button {
  width: 100%;
  padding: 1rem 2rem;
  background: none;
  border: none;
  color: var(--color-white);
  text-align: left;
  cursor: pointer;
  font-size: 1rem;
  transition: all 0.3s ease;
  position: relative;
}

.nav-button:hover {
  background: rgba(255, 255, 255, 0.1);
}

.nav-button--active {
  background: var(--color-secondary);
  color: var(--color-white);
}

.nav-button--active::before {
  content: '';
  position: absolute;
  left: 0;
  top: 0;
  bottom: 0;
  width: 4px;
  background: var(--color-accent);
}

.main {
  flex: 1;
  margin-left: var(--sidebar-width);
  padding: 2rem;
  transition: margin-left 0.3s ease;
}

.tool-container {
  max-width: 800px;
  margin: 0 auto;
}

.overlay {
  display: none;
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0, 0, 0, 0.5);
  z-index: 999;
}

/* Mobile styles */
@media (max-width: 768px) {
  .menu-toggle {
    display: flex;
  }

  .sidebar {
    transform: translateX(-100%);
  }

  .sidebar--open {
    transform: translateX(0);
  }

  .main {
    margin-left: 0;
    padding: 5rem 1rem 2rem;
  }

  .overlay {
    display: block;
  }
}

/* Tablet styles */
@media (max-width: 1024px) and (min-width: 769px) {
  .main {
    padding: 2rem 1.5rem;
  }
}
</style>
