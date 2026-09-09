<script setup>
import { ref, computed, onMounted, onUnmounted } from 'vue'
import RatioConverter from './components/RatioConverter.vue'
import PixelToEm from './components/PixelToEm.vue'
import LineHeightCalculator from './components/LineHeightCalculator.vue'
import FigmaType from './components/FigmaType.vue'
import ImageToWebP from './components/ImageToWebP.vue'
import TextCompare from './components/TextCompare.vue'
import HelpfulLinks from './components/HelpfulLinks.vue'

const currentTool = ref('ratio')
const handleOpen = ref(false)

const tools = [
  { id: 'ratio', name: 'Ratio Converter', component: RatioConverter },
  { id: 'pixel-em', name: 'Pixel to Em', component: PixelToEm },
  { id: 'line-height', name: 'Line Height Calculator', component: LineHeightCalculator },
  { id: 'figma-type', name: 'Figma Type', component: FigmaType },
  { id: 'image-webp', name: 'Image Converter', component: ImageToWebP },
  { id: 'text-compare', name: 'Text Compare', component: TextCompare },
  { id: 'helpful-links', name: 'Helpful Links', component: HelpfulLinks }
]

const activeTool = computed(() => tools.find((tool) => tool.id === currentTool.value))

const getToolFromHash = () => {
  const hash = window.location.hash.substring(1)
  const validTool = tools.find((tool) => tool.id === hash)
  return validTool ? hash : 'ratio'
}

const updateHash = (toolId) => {
  window.location.hash = toolId
}

const handleHashChange = () => {
  const toolId = getToolFromHash()
  if (toolId !== currentTool.value) {
    currentTool.value = toolId
    handleOpen.value = false
  }
}

const selectTool = (toolId) => {
  currentTool.value = toolId
  handleOpen.value = false
  updateHash(toolId)
}

const toggleHandle = () => {
  handleOpen.value = !handleOpen.value
}

const closeHandle = () => {
  handleOpen.value = false
}

const onKeydown = (event) => {
  if (event.key === 'Escape' && handleOpen.value) {
    handleOpen.value = false
  }
}

onMounted(() => {
  currentTool.value = getToolFromHash()
  window.addEventListener('hashchange', handleHashChange)
  window.addEventListener('keydown', onKeydown)
})

onUnmounted(() => {
  window.removeEventListener('hashchange', handleHashChange)
  window.removeEventListener('keydown', onKeydown)
})
</script>

<template>
  <div class="bench">
    <a class="skip-link" href="#tool">Skip to tool</a>

    <button
      class="handle-stub"
      type="button"
      :class="{ 'handle-stub--tucked': handleOpen }"
      :aria-expanded="handleOpen"
      aria-controls="tool-handle"
      @click="toggleHandle"
    >
      <span class="handle-stub__label">Tools</span>
    </button>

    <aside
      id="tool-handle"
      class="handle"
      :class="{ 'handle--open': handleOpen }"
    >
      <h1 class="handle__wordmark">Web Dev Tools</h1>
      <nav class="handle__nav" aria-label="Tools">
        <ul class="implements">
          <li v-for="tool in tools" :key="tool.id">
            <button
              type="button"
              class="implement"
              :class="{ 'implement--open': currentTool === tool.id }"
              :aria-current="currentTool === tool.id ? 'page' : undefined"
              @click="selectTool(tool.id)"
            >
              {{ tool.name }}
            </button>
          </li>
        </ul>
      </nav>
    </aside>

    <main id="tool" class="stage" :class="{ 'stage--wide': currentTool === 'text-compare' }">
      <div class="sheet" :key="currentTool">
        <component :is="activeTool?.component" />
      </div>
    </main>

    <div
      v-if="handleOpen"
      class="overlay"
      @click="closeHandle"
    ></div>
  </div>
</template>

<style>
:root {
  --color-chart-navy: #003049;
  --color-signal-red: #d62828;
  --color-safety-orange: #f77f00;
  --color-flag-gold: #fcbf49;
  --color-chart-paper: #f8e7c1;
  --color-white: #ffffff;
  --color-ink: #2c3e50;
  --color-quiet-ink: #6c757d;
  --color-hairline: #e1e5e9;
  --color-inset-paper: #f8f9fa;
  --color-code-slate: #2d3748;
  --color-primary: var(--color-chart-navy);
  --color-secondary: var(--color-signal-red);
  --color-accent: var(--color-safety-orange);
  --color-accent-light: var(--color-flag-gold);
  --color-background: var(--color-chart-paper);
  --color-text: var(--color-ink);
  --color-text-light: var(--color-quiet-ink);
  --handle-width: 13.5rem;
  --ease-standard: 200ms ease-out;
  --font-sans: "IBM Plex Sans", "IBM Plex Sans Fallback", -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif;
  --font-mono: "IBM Plex Mono", "IBM Plex Mono Fallback", Monaco, Menlo, Ubuntu Mono, monospace;
}

@font-face {
  font-family: "IBM Plex Sans Fallback";
  src: local("Arial");
  size-adjust: 105.4%;
  ascent-override: 97%;
  descent-override: 26%;
  line-gap-override: 0%;
}

@font-face {
  font-family: "IBM Plex Mono Fallback";
  src: local("Courier New");
  size-adjust: 99.2%;
  ascent-override: 102%;
  descent-override: 32%;
  line-gap-override: 0%;
}

* {
  box-sizing: border-box;
}

html {
  scrollbar-color: var(--color-chart-navy) var(--color-chart-paper);
}

body {
  margin: 0;
  font-family: var(--font-sans);
  background-color: var(--color-chart-paper);
  color: var(--color-ink);
  line-height: 1.6;
  caret-color: var(--color-safety-orange);
}

::selection {
  background: var(--color-safety-orange);
  color: var(--color-chart-navy);
}

:focus-visible {
  outline: 2px solid var(--color-safety-orange);
  outline-offset: 2px;
}

a {
  text-underline-offset: 0.2em;
}

.skip-link {
  position: absolute;
  inset-inline-start: 1rem;
  inset-block-start: -4rem;
  z-index: 2000;
  padding: 0.5rem 1rem;
  background: var(--color-chart-navy);
  color: var(--color-white);
  text-decoration: none;
  font-weight: 600;
}

.skip-link:focus {
  inset-block-start: 1rem;
}

.bench {
  display: flex;
  min-height: 100vh;
  position: relative;
}

.handle-stub {
  display: none;
  position: fixed;
  inset-block: 0;
  inset-inline-start: 0;
  z-index: 1001;
  width: 2.75rem;
  padding: 0;
  background: var(--color-chart-navy);
  color: var(--color-flag-gold);
  border: none;
  border-radius: 0;
  font-family: inherit;
  font-size: 0.9rem;
  font-weight: 600;
  letter-spacing: 0.12em;
  cursor: pointer;
  transition: background-color var(--ease-standard);
}

.handle-stub:hover,
.handle-stub:focus-visible {
  background: var(--color-signal-red);
  color: var(--color-white);
}

.handle-stub--tucked {
  visibility: hidden;
}

.handle-stub__label {
  display: block;
  writing-mode: vertical-rl;
  transform: rotate(180deg);
  padding-block: 1.5rem;
}

.handle {
  width: var(--handle-width);
  background: var(--color-chart-navy);
  color: var(--color-white);
  padding: 1.5rem 0 2rem;
  position: fixed;
  inset-inline-start: 0;
  inset-block-start: 0;
  height: 100vh;
  overflow-y: auto;
  z-index: 1000;
  border-radius: 0;
}

.handle__wordmark {
  margin: 0 1rem 1.5rem;
  padding: 0 0.25rem;
  font-size: 1.5rem;
  font-weight: 600;
  line-height: 1.35;
  letter-spacing: 0.01em;
  color: var(--color-flag-gold);
  text-wrap: balance;
}

.handle__nav {
  padding: 0;
}

.implements {
  list-style: none;
  padding: 0;
  margin: 0;
}

.implements li + li {
  margin-block-start: 0.15rem;
}

.implement {
  width: 100%;
  min-height: 44px;
  padding: 0.65rem 1rem 0.65rem 1.15rem;
  background: none;
  border: none;
  color: var(--color-white);
  text-align: left;
  cursor: pointer;
  font-family: inherit;
  font-size: 1rem;
  font-weight: 500;
  line-height: 1.4;
  letter-spacing: 0.01em;
  transition: background-color var(--ease-standard), color var(--ease-standard);
  position: relative;
}

.implement:hover {
  background: rgba(255, 255, 255, 0.1);
}

.implement:focus-visible {
  outline-offset: -4px;
}

.implement--open {
  background: var(--color-signal-red);
  color: var(--color-white);
  font-weight: 600;
}

.implement--open::before {
  content: "";
  position: absolute;
  inset-block: 0;
  inset-inline-start: 0;
  width: 4px;
  background: var(--color-safety-orange);
  box-shadow: 4px 0 0 var(--color-chart-navy);
}

.stage {
  flex: 1;
  margin-inline-start: var(--handle-width);
  padding: 1.5rem 1.5rem 1.5rem 0;
  min-width: 0;
}

.stage--wide .sheet {
  max-width: 1100px;
}

.sheet {
  max-width: 800px;
  min-height: calc(100vh - 3rem);
  background: var(--color-white);
  color: var(--color-ink);
  border-radius: 0 12px 12px 0;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  animation: unfold var(--ease-standard);
  transform-origin: left center;
}

@keyframes unfold {
  from {
    clip-path: inset(0 100% 0 0);
  }
  to {
    clip-path: inset(0 0 0 0);
  }
}

@media (prefers-reduced-motion: reduce) {
  .sheet {
    animation: none;
  }

  .implement,
  .handle-stub,
  .handle {
    transition: none;
  }
}

.overlay {
  display: none;
  position: fixed;
  inset: 0;
  background: rgba(0, 48, 73, 0.55);
  z-index: 999;
}

@media (max-width: 768px) {
  .handle-stub {
    display: flex;
  }

  .handle {
    transform: translateX(-100%);
    transition: transform var(--ease-standard);
  }

  .handle--open {
    transform: translateX(0);
  }

  .stage {
    margin-inline-start: 0;
    padding: 1.5rem 1rem 1.5rem 3.5rem;
  }

  .sheet {
    max-width: none;
    min-height: calc(100vh - 3rem);
    border-radius: 0 12px 12px 0;
  }

  .overlay {
    display: block;
  }
}

@media (max-width: 1024px) and (min-width: 769px) {
  .stage {
    padding: 1.5rem 1.25rem 1.5rem 0;
  }
}

/* Shared Component Styles */
.tool {
  background: transparent;
  border-radius: 0;
  padding: 2rem;
  box-shadow: none;
}

.tool__header {
  margin-bottom: 2rem;
}

.tool__title {
  color: var(--color-chart-navy);
  margin: 0 0 0.5rem 0;
  font-size: 2rem;
  font-weight: 600;
  line-height: 1.2;
  text-wrap: balance;
  overflow-wrap: break-word;
}

.tool__description {
  color: var(--color-quiet-ink);
  margin: 0;
  font-size: 1rem;
  max-width: 65ch;
}

.section-title {
  color: var(--color-chart-navy);
  margin: 1.5rem 0 1rem 0;
  font-size: 1.25rem;
  font-weight: 600;
  line-height: 1.3;
  text-wrap: balance;
}

.input-group {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
}

.input-label {
  font-weight: 500;
  color: var(--color-ink);
  font-size: 0.9rem;
  line-height: 1.4;
}

.input-with-unit {
  position: relative;
  display: inline-flex;
  align-items: center;
  justify-content: flex-start;
}

.input {
  padding: 0.75rem;
  border: 2px solid var(--color-hairline);
  border-radius: 8px;
  font-size: 1rem;
  font-family: inherit;
  color: var(--color-ink);
  background: var(--color-white);
  transition: border-color 0.3s ease, box-shadow 0.3s ease;
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

.input:hover:not(:focus) {
  border-color: var(--color-flag-gold);
}

.input:focus {
  outline: none;
  border-color: var(--color-safety-orange);
  box-shadow: 0 0 0 3px rgba(247, 127, 0, 0.1);
}

.input::placeholder,
textarea::placeholder {
  color: var(--color-quiet-ink);
  opacity: 1;
}

.unit {
  color: var(--color-quiet-ink);
  font-weight: 500;
  margin-inline-start: 0.75em;
  pointer-events: none;
}

.preview-box {
  background: var(--color-inset-paper);
  border: 1px solid var(--color-hairline);
  border-radius: 8px;
  padding: 1.5rem;
  margin: 1em 0 1em 0;
}

.preview-box__title {
  margin: 0 0 1em 0;
  color: var(--color-chart-navy);
  font-size: 1.25rem;
  font-weight: 600;
  line-height: 1.3;
}

.code-preview {
  background: var(--color-code-slate);
  color: #e2e8f0;
  padding: 1rem;
  border-radius: 6px;
  font-family: var(--font-mono);
  font-size: 0.9rem;
  line-height: 1.55;
  letter-spacing: 0.01em;
  margin-bottom: 1rem;
  overflow-x: auto;
}

.calculation-display {
  padding: 1rem;
  background: var(--color-chart-paper);
  border-radius: 8px;
}

.calculation__formula {
  font-family: var(--font-mono);
  font-size: 1rem;
  color: var(--color-chart-navy);
  font-weight: 600;
}

.copy-button {
  background: var(--color-safety-orange);
  color: var(--color-white);
  border: none;
  padding: 0.5rem 1rem;
  border-radius: 6px;
  cursor: pointer;
  font-family: inherit;
  font-size: 0.9rem;
  font-weight: 600;
  transition: background-color var(--ease-standard);
}

.copy-button:hover {
  background: var(--color-signal-red);
}

.copy-button:focus-visible {
  outline-offset: 2px;
}

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

.result-card {
  padding: 1.5rem;
  border-radius: 8px;
  text-align: center;
}

.result-card--primary {
  background: var(--color-chart-navy);
  color: var(--color-white);
}

.result-card--secondary {
  background: var(--color-signal-red);
  color: var(--color-white);
}

.result-card--accent {
  background: var(--color-safety-orange);
  color: var(--color-white);
}

.result-label {
  font-size: 0.9rem;
  font-weight: 500;
  line-height: 1.45;
  letter-spacing: 0.01em;
  margin-bottom: 0.5rem;
}

.result-value {
  font-size: 2rem;
  font-weight: 700;
  line-height: 1.2;
  margin-bottom: 0.5rem;
  font-variant-numeric: tabular-nums;
}

.result-description {
  font-size: 0.9rem;
  font-weight: 500;
  line-height: 1.45;
  letter-spacing: 0.01em;
}

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
    font-size: 0.9rem;
  }
}
</style>
