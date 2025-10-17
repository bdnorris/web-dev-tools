<template>
  <div class="tool">
    <div class="tool__header">
      <h2 class="tool__title">Figma Type Cleaner</h2>
      <p class="tool__description">
        Paste in Figma typography CSS and get cleaner, more useful code with em units, fractional line-heights, and unnecessary properties removed.
      </p>
    </div>

    <div class="figma-type-cleaner">
      <!-- Base Size Input -->
      <div class="base-size-section">
        <h3 class="section-title">Base Font Size</h3>
        <div class="input-group">
          <label for="base-size" class="input-label">Base Size (pixels)</label>
          <div class="input-with-unit">
            <input 
              id="base-size"
              v-model.number="baseSize" 
              type="number" 
              class="input input--with-unit input--medium"
              min="1"
              @input="processCSS"
            />
            <span class="unit">px</span>
          </div>
        </div>
      </div>

      <!-- Input Section -->
      <div class="input-section">
        <h3 class="section-title">Figma CSS Input</h3>
        <div class="textarea-group">
          <label for="figma-css" class="input-label">Paste Figma CSS here</label>
          <textarea
            id="figma-css"
            v-model="figmaCSS"
            class="css-textarea"
            placeholder="color: var(--Primary-White, #FFF);
text-align: center;

/* Body/L */
font-family: Karla;
font-size: 22px;
font-style: normal;
font-weight: 400;
line-height: 140%; /* 30.8px */"
            @input="processCSS"
          ></textarea>
        </div>
      </div>

      <!-- Output Section -->
      <div v-if="processedCSS" class="output-section">
        <h3 class="section-title">Cleaned CSS Output</h3>
        
        <div class="preview-box">
          <h4 class="preview-box__title">Processed CSS</h4>
          <div class="code-preview">
            <pre><code>{{ processedCSS }}</code></pre>
          </div>
          <button @click="copyCSS" class="copy-button">
            {{ copied ? 'Copied!' : 'Copy CSS' }}
          </button>
        </div>

        <!-- Changes Summary -->
        <div v-if="changesSummary.length > 0" class="changes-summary">
          <h4 class="preview-box__title">Changes Made</h4>
          <ul class="changes-list">
            <li v-for="change in changesSummary" :key="change" class="change-item">
              {{ change }}
            </li>
          </ul>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

const baseSize = ref(16)
const figmaCSS = ref('')
const copied = ref(false)

const changesSummary = ref([])

// Convert pixels to em
const pxToEm = (pixels) => {
  return Math.round((pixels / baseSize.value) * 1000) / 1000
}

// Convert percentage to decimal for line-height
const percentageToDecimal = (percentage) => {
  const num = parseFloat(percentage.replace('%', ''))
  return Math.round((num / 100) * 1000) / 1000
}



const processedCSS = computed(() => {
  if (!figmaCSS.value.trim()) return ''
  
  const changes = []
  let css = figmaCSS.value
  
  // Remove comments
  css = css.replace(/\/\*[^*]*\*+(?:[^/*][^*]*\*+)*\//g, '')
  changes.push('Removed CSS comments')
  
  // Split into lines and process each
  const lines = css.split('\n').map(line => line.trim()).filter(line => line)
  const processedLines = []
  
  for (const line of lines) {
    if (!line.includes(':')) continue
    
    const [property, value] = line.split(':').map(s => s.trim())
    const cleanValue = value.replace(';', '').trim()
    
    // Skip font-family
    if (property === 'font-family') {
      changes.push('Removed font-family property')
      continue
    }
    
    // Skip font-style if normal
    if (property === 'font-style' && cleanValue === 'normal') {
      changes.push('Removed font-style: normal')
      continue
    }
    
    // Convert font-size to em
    if (property === 'font-size') {
      const match = cleanValue.match(/(\d+(?:\.\d+)?)px/)
      if (match) {
        const pixels = parseFloat(match[1])
        const ems = pxToEm(pixels)
        processedLines.push(`${property}: ${ems}em;`)
        changes.push(`Converted font-size from ${pixels}px to ${ems}em`)
        continue
      }
    }
    
    // Convert line-height percentage to decimal
    if (property === 'line-height') {
      if (cleanValue.includes('%')) {
        const decimal = percentageToDecimal(cleanValue)
        processedLines.push(`${property}: ${decimal};`)
        changes.push(`Converted line-height from ${cleanValue} to ${decimal}`)
        continue
      }
    }
    
    // Convert letter-spacing to em
    if (property === 'letter-spacing') {
      const match = cleanValue.match(/(-?\d+(?:\.\d+)?)px/)
      if (match) {
        const pixels = parseFloat(match[1])
        const ems = pxToEm(pixels)
        processedLines.push(`${property}: ${ems}em;`)
        changes.push(`Converted letter-spacing from ${pixels}px to ${ems}em`)
        continue
      }
    }
    
    // Keep other properties as-is
    processedLines.push(`${property}: ${cleanValue}${cleanValue.endsWith(';') ? '' : ';'}`)
  }
  
  changesSummary.value = [...new Set(changes)] // Remove duplicates
  
  return processedLines.join('\n')
})

const processCSS = () => {
  // Trigger computed property recalculation
  // This happens automatically when figmaCSS or baseSize changes
}

const copyCSS = async () => {
  try {
    await navigator.clipboard.writeText(processedCSS.value)
    copied.value = true
    setTimeout(() => {
      copied.value = false
    }, 2000)
  } catch (err) {
    console.error('Failed to copy CSS:', err)
  }
}
</script>

<style scoped>
.figma-type-cleaner {
  display: flex;
  flex-direction: column;
  gap: 2.5rem;
}

.textarea-group {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
}

.css-textarea {
  width: 100%;
  min-height: 200px;
  padding: 1rem;
  border: 2px solid #e1e5e9;
  border-radius: 8px;
  font-family: 'Monaco', 'Menlo', 'Ubuntu Mono', monospace;
  font-size: 0.9rem;
  line-height: 1.5;
  resize: vertical;
  transition: all 0.3s ease;
}

.css-textarea:focus {
  outline: none;
  border-color: var(--color-accent);
  box-shadow: 0 0 0 3px rgba(247, 127, 0, 0.1);
}

.css-textarea::placeholder {
  color: var(--color-text-light);
  opacity: 0.7;
}

.changes-summary {
  background: var(--color-background);
  border: 1px solid #e1e5e9;
  border-radius: 8px;
  padding: 1.5rem;
  margin-top: 1rem;
}

.changes-list {
  margin: 0.5rem 0 0 0;
  padding-left: 1.5rem;
}

.change-item {
  color: var(--color-text);
  margin-bottom: 0.25rem;
  font-size: 0.9rem;
}

@media (max-width: 768px) {
  .css-textarea {
    min-height: 150px;
    font-size: 0.8rem;
  }
}
</style>