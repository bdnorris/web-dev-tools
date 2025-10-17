<template>
  <div class="tool">
    <div class="tool__header">
      <h2 class="tool__title">Line Height Calculator</h2>
      <p class="tool__description">
        Calculate fractional line height values from pixel measurements. Perfect for maintaining consistent vertical rhythm in your designs.
      </p>
    </div>

    <div class="line-height-calculator">
      <!-- Input Section -->
      <div class="input-section">
        <h3 class="section-title">Font & Line Height Measurements</h3>
        <div class="inputs-grid">
          <div class="input-group">
            <label for="font-size" class="input-label">Font Size</label>
            <div class="input-with-unit">
              <input 
                id="font-size"
                v-model.number="fontSize" 
                type="number" 
                class="input"
                placeholder="16"
                min="1"
                @input="calculateLineHeight"
              />
              <span class="unit">px</span>
            </div>
          </div>

          <div class="input-group">
            <label for="line-height-px" class="input-label">Line Height</label>
            <div class="input-with-unit">
              <input 
                id="line-height-px"
                v-model.number="lineHeightPx" 
                type="number" 
                class="input"
                placeholder="24"
                min="1"
                @input="calculateLineHeight"
              />
              <span class="unit">px</span>
            </div>
          </div>
        </div>
      </div>

      <!-- Results Section -->
      <div v-if="fontSize && lineHeightPx" class="results-section">
        <h3 class="section-title">Results</h3>
        
        <div class="results-grid">
          <!-- Decimal Result -->
          <div class="result-card result-card--primary">
            <div class="result-label">Decimal Line Height</div>
            <div class="result-value">{{ decimalLineHeight }}</div>
            <div class="result-description">Use this value in CSS</div>
          </div>

          <!-- Fraction Result -->
          <div class="result-card result-card--secondary">
            <div class="result-label">Fractional Line Height</div>
            <div class="result-value">{{ fractionLineHeight }}</div>
            <div class="result-description">Simplified fraction</div>
          </div>

          <!-- Percentage Result -->
          <div class="result-card result-card--accent">
            <div class="result-label">Percentage Line Height</div>
            <div class="result-value">{{ percentageLineHeight }}%</div>
            <div class="result-description">Alternative format</div>
          </div>
        </div>

        <!-- CSS Preview -->
        <div class="css-preview">
          <h4 class="css-title">CSS Code</h4>
          <div class="css-code">
            <code>
font-size: {{ fontSize }}px;<br>
line-height: {{ decimalLineHeight }}; /* {{ fractionLineHeight }} */
            </code>
          </div>
          <button @click="copyCss" class="copy-button">
            {{ copied ? 'Copied!' : 'Copy CSS' }}
          </button>
        </div>

        <!-- Visual Preview -->
        <div class="visual-preview">
          <h4 class="preview-title">Visual Preview</h4>
          <div 
            class="preview-text" 
            :style="{ fontSize: fontSize + 'px', lineHeight: decimalLineHeight }"
          >
            <p>This is how your text will look with the calculated line height.</p>
            <p>Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.</p>
            <p>Notice the spacing between these lines matches your specified measurements.</p>
          </div>
        </div>
      </div>

      <!-- Common Ratios -->
      <div class="common-ratios-section">
        <h3 class="section-title">Common Line Height Ratios</h3>
        <div class="ratios-grid">
          <div 
            v-for="ratio in commonRatios" 
            :key="ratio.value"
            class="ratio-card"
            @click="applyRatio(ratio.value)"
          >
            <div class="ratio-value">{{ ratio.value }}</div>
            <div class="ratio-name">{{ ratio.name }}</div>
            <div class="ratio-usage">{{ ratio.usage }}</div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

const fontSize = ref(16)
const lineHeightPx = ref(24)
const copied = ref(false)

const commonRatios = [
  { value: 1.2, name: 'Tight', usage: 'Headlines, UI elements' },
  { value: 1.4, name: 'Comfortable', usage: 'Body text, short paragraphs' },
  { value: 1.5, name: 'Standard', usage: 'Most body text' },
  { value: 1.6, name: 'Relaxed', usage: 'Long-form reading' },
  { value: 1.8, name: 'Loose', usage: 'Accessibility, dyslexia-friendly' }
]

const decimalLineHeight = computed(() => {
  if (!fontSize.value || !lineHeightPx.value) return ''
  return Math.round((lineHeightPx.value / fontSize.value) * 1000) / 1000
})

const percentageLineHeight = computed(() => {
  if (!decimalLineHeight.value) return ''
  return Math.round(decimalLineHeight.value * 100)
})

const fractionLineHeight = computed(() => {
  if (!fontSize.value || !lineHeightPx.value) return ''
  
  const gcd = (a, b) => {
    return b === 0 ? a : gcd(b, a % b)
  }
  
  const numerator = lineHeightPx.value
  const denominator = fontSize.value
  const commonDivisor = gcd(numerator, denominator)
  
  const simplifiedNum = numerator / commonDivisor
  const simplifiedDen = denominator / commonDivisor
  
  if (simplifiedDen === 1) {
    return simplifiedNum.toString()
  }
  
  return `${simplifiedNum}/${simplifiedDen}`
})

const calculateLineHeight = () => {
  // Calculation happens automatically via computed properties
}

const applyRatio = (ratio) => {
  if (fontSize.value) {
    lineHeightPx.value = Math.round(fontSize.value * ratio)
  }
}

const copyCss = async () => {
  const cssText = `font-size: ${fontSize.value}px;\nline-height: ${decimalLineHeight.value}; /* ${fractionLineHeight.value} */`
  
  try {
    await navigator.clipboard.writeText(cssText)
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

.line-height-calculator {
  display: flex;
  flex-direction: column;
  gap: 2.5rem;
}

.section-title {
  color: var(--color-primary);
  margin: 0 0 1rem 0;
  font-size: 1.25rem;
  font-weight: 600;
}

.inputs-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  gap: 1.5rem;
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
}

.input {
  padding: 0.75rem;
  padding-right: 3rem;
  border: 2px solid #e1e5e9;
  border-radius: 8px;
  font-size: 1rem;
  width: 100%;
  transition: all 0.3s ease;
}

.input:focus {
  outline: none;
  border-color: var(--color-accent);
  box-shadow: 0 0 0 3px rgba(247, 127, 0, 0.1);
}

.unit {
  position: absolute;
  right: 0.75rem;
  color: var(--color-text-light);
  font-weight: 500;
  pointer-events: none;
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

.css-preview {
  background: #f8f9fa;
  border: 1px solid #e1e5e9;
  border-radius: 8px;
  padding: 1.5rem;
}

.css-title {
  margin: 0 0 1rem 0;
  color: var(--color-primary);
  font-size: 1.1rem;
}

.css-code {
  background: #2d3748;
  color: #e2e8f0;
  padding: 1rem;
  border-radius: 6px;
  font-family: 'Monaco', 'Menlo', 'Ubuntu Mono', monospace;
  font-size: 0.9rem;
  line-height: 1.5;
  margin-bottom: 1rem;
  overflow-x: auto;
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

.visual-preview {
  background: #f8f9fa;
  border: 1px solid #e1e5e9;
  border-radius: 8px;
  padding: 1.5rem;
}

.preview-title {
  margin: 0 0 1rem 0;
  color: var(--color-primary);
  font-size: 1.1rem;
}

.preview-text {
  max-width: 100%;
  color: var(--color-text);
}

.preview-text p {
  margin: 0 0 1em 0;
}

.ratios-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
  gap: 1rem;
}

.ratio-card {
  background: var(--color-background);
  border: 2px solid transparent;
  border-radius: 8px;
  padding: 1.5rem;
  text-align: center;
  cursor: pointer;
  transition: all 0.3s ease;
}

.ratio-card:hover {
  border-color: var(--color-accent);
  background: var(--color-white);
}

.ratio-value {
  font-size: 1.5rem;
  font-weight: 700;
  color: var(--color-primary);
  margin-bottom: 0.5rem;
}

.ratio-name {
  font-weight: 600;
  color: var(--color-text);
  margin-bottom: 0.25rem;
}

.ratio-usage {
  font-size: 0.8rem;
  color: var(--color-text-light);
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
  
  .ratios-grid {
    grid-template-columns: 1fr;
  }
  
  .css-code {
    font-size: 0.8rem;
  }
}
</style>