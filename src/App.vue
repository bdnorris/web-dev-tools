<script setup>
import { ref, onMounted } from 'vue'
import RatioConverter from './components/RatioConverter.vue'
import PixelToEm from './components/PixelToEm.vue'
import LineHeightCalculator from './components/LineHeightCalculator.vue'
import FigmaType from './components/FigmaType.vue'

const currentTool = ref('ratio')
const menuOpen = ref(false)

const tools = [
  { id: 'ratio', name: 'Ratio Converter', component: RatioConverter },
  { id: 'pixel-em', name: 'Pixel to Em', component: PixelToEm },
  { id: 'line-height', name: 'Line Height Calculator', component: LineHeightCalculator },
  { id: 'figma-type', name: 'Figma Type', component: FigmaType }
]

const toggleMenu = () => {
  menuOpen.value = !menuOpen.value
}

// Get tool ID from URL hash
const getToolFromHash = () => {
  const hash = window.location.hash.substring(1) // Remove the # symbol
  const validTool = tools.find(tool => tool.id === hash)
  return validTool ? hash : 'ratio' // Default to 'ratio' if invalid hash
}

// Update URL hash when tool changes
const updateHash = (toolId) => {
  window.location.hash = toolId
}

// Handle hash changes (back/forward navigation)
const handleHashChange = () => {
  const toolId = getToolFromHash()
  if (toolId !== currentTool.value) {
    currentTool.value = toolId
    menuOpen.value = false
  }
}

const selectTool = (toolId) => {
  currentTool.value = toolId
  menuOpen.value = false
  updateHash(toolId)
}

// Initialize tool from URL hash on load
onMounted(() => {
  currentTool.value = getToolFromHash()
  
  // Listen for hash changes (back/forward navigation)
  window.addEventListener('hashchange', handleHashChange)
})
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
  font-family: 'IBM Plex Sans', -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, sans-serif;
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
  inset-block-start: 1rem;
  inset-inline-end: 1rem;
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

/* Shared Component Styles */
.tool {
  background: var(--color-white);
  border-radius: 12px;
  padding: 2rem;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

.tool__header {
  margin-bottom: 2rem;
}

.tool__title {
  color: var(--color-primary);
  margin: 0 0 0.5rem 0;
  font-size: 2rem;
  font-weight: 600;
}

.tool__description {
  color: var(--color-text-light);
  margin: 0;
  font-size: 1.1rem;
}

.section-title {
  color: var(--color-primary);
  margin: 0 0 1rem 0;
  font-size: 1.25rem;
  font-weight: 600;
}

.input-group {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
}

.input-label {
  font-weight: 500;
  color: var(--color-text);
  font-size: 0.9rem;
}

.input-with-unit {
  position: relative;
  display: inline-flex;
  align-items: center;
  justify-content: flex-start;
}

.input {
  padding: 0.75rem;
  border: 2px solid #e1e5e9;
  border-radius: 8px;
  font-size: 1rem;
  transition: all 0.3s ease;
}

.input--with-unit {
  padding-right: 3rem;
}

.input--small {
  width: 80px;
}

.input--medium {
  width: 150px;
}

.input--large {
  width: 200px;
}

.input--full {
  width: 100%;
}

.input:focus {
  outline: none;
  border-color: var(--color-accent);
  box-shadow: 0 0 0 3px rgba(247, 127, 0, 0.1);
}

.unit {
  color: var(--color-text-light);
  font-weight: 500;
  margin-inline-start: 0.75em;
  pointer-events: none;
}

/* Preview Box Styles */
.preview-box {
  background: #f8f9fa;
  border: 1px solid #e1e5e9;
  border-radius: 8px;
  padding: 1.5rem;
  margin: 1em 0 1em 0;
}

.preview-box__title {
  margin: 0 0 1em 0;
  color: var(--color-primary);
  font-size: 1.1rem;
}

.code-preview {
  background: #2d3748;
  color: #e2e8f0;
  padding: 1rem;
  border-radius: 6px;
  font-family: 'IBM Plex Mono', 'Monaco', 'Menlo', 'Ubuntu Mono', monospace;
  font-size: 0.9rem;
  line-height: 1.5;
  margin-bottom: 1rem;
  overflow-x: auto;
}

.calculation-display {
  padding: 1rem;
  background: var(--color-background);
  border-radius: 8px;
  border-left: 4px solid var(--color-accent);
}

.calculation__formula {
  font-family: 'IBM Plex Mono', 'Monaco', 'Menlo', 'Ubuntu Mono', monospace;
  font-size: 1rem;
  color: var(--color-primary);
  font-weight: 600;
}

.copy-button {
  background: var(--color-accent);
  color: var(--color-white);
  border: none;
  padding: 0.5rem 1rem;
  border-radius: 6px;
  cursor: pointer;
  font-size: 0.9rem;
  transition: background-color 0.3s ease;
}

.copy-button:hover {
  background: var(--color-secondary);
}

/* Grid Layouts */
.inputs-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  gap: 1.5rem;
}

.results-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 1.5rem;
}

/* Result Cards */
.result-card {
  padding: 1.5rem;
  border-radius: 8px;
  text-align: center;
}

.result-card--primary {
  background: var(--color-primary);
  color: var(--color-white);
}

.result-card--secondary {
  background: var(--color-secondary);
  color: var(--color-white);
}

.result-card--accent {
  background: var(--color-accent);
  color: var(--color-white);
}

.result-label {
  font-size: 0.9rem;
  opacity: 0.9;
  margin-bottom: 0.5rem;
}

.result-value {
  font-size: 2rem;
  font-weight: 700;
  margin-bottom: 0.5rem;
}

.result-description {
  font-size: 0.8rem;
  opacity: 0.8;
}

/* Mobile Responsive Adjustments */
@media (max-width: 768px) {
  .tool {
    padding: 1.5rem;
  }
  
  .inputs-grid {
    grid-template-columns: 1fr;
  }
  
  .results-grid {
    grid-template-columns: 1fr;
  }
  
  .input--medium,
  .input--large {
    width: 100%;
    max-width: 200px;
  }
  
  .code-preview {
    font-size: 0.8rem;
  }
}
</style>
