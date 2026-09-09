<template>
  <div class="tool">
    <div class="tool__header">
      <h2 class="tool__title">Line Height Calculator</h2>
      <p class="tool__description">
        From two pixel measurements.
      </p>
    </div>

    <div class="line-height-calculator">
      <!-- Input Section -->
      <div class="input-section">
        <h3 class="section-title">Measurements</h3>
        <div class="inputs-grid">
          <div class="input-group">
            <label for="font-size" class="input-label">Font size</label>
            <div class="input-with-unit">
              <input 
                id="font-size"
                v-model.number="fontSize" 
                type="number" 
                class="input input--with-unit input--full"
                placeholder="16"
                min="1"
                @input="calculateLineHeight"
              />
              <span class="unit">px</span>
            </div>
          </div>

          <div class="input-group">
            <label for="line-height-px" class="input-label">Line height</label>
            <div class="input-with-unit">
              <input 
                id="line-height-px"
                v-model.number="lineHeightPx" 
                type="number" 
                class="input input--with-unit input--full"
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
            <div class="result-label">Decimal</div>
            <div class="result-value">{{ decimalLineHeight }}</div>
            <div class="result-description">CSS line-height</div>
          </div>

          <!-- Fraction Result -->
          <div class="result-card result-card--secondary">
            <div class="result-label">Fraction</div>
            <div class="result-value">{{ fractionLineHeight }}</div>
            <div class="result-description">Same ratio, as a fraction</div>
          </div>

          <!-- Percentage Result -->
          <div class="result-card result-card--accent">
            <div class="result-label">Percent</div>
            <div class="result-value">{{ percentageLineHeight }}%</div>
            <div class="result-description">Same ratio, as a percent</div>
          </div>
        </div>

        <!-- CSS Preview -->
        <div class="preview-box">
          <h4 class="preview-box__title">CSS</h4>
          <div class="code-preview">
            <code>
font-size: {{ fontSize }}px;<br>
line-height: {{ decimalLineHeight }}; /* {{ fractionLineHeight }} */
            </code>
          </div>
          <button type="button" @click="copyCss" class="copy-button" aria-live="polite">
            {{ copied ? 'Copied!' : 'Copy CSS' }}
          </button>
        </div>

        <!-- Visual Preview -->
        <div class="preview-box">
          <h4 class="preview-box__title">Preview</h4>
          <div 
            class="preview-text" 
            :style="{ fontSize: fontSize + 'px', lineHeight: decimalLineHeight }"
          >
            <p>Body copy used to check leading.</p>
            <p>A second line so the gap between them is visible.</p>
            <p>A third line for a full paragraph rhythm.</p>
          </div>
        </div>
      </div>

      <!-- Common Ratios -->
      <div class="common-ratios-section">
        <h3 class="section-title">Common ratios</h3>
        <div class="ratios-grid">
          <button 
            v-for="ratio in commonRatios" 
            :key="ratio.value"
            type="button"
            class="ratio-card"
            @click="applyRatio(ratio.value)"
          >
            <div class="ratio-value">{{ ratio.value }}</div>
            <div class="ratio-name">{{ ratio.name }}</div>
            <div class="ratio-usage">{{ ratio.usage }}</div>
          </button>
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
.line-height-calculator {
  display: flex;
  flex-direction: column;
  gap: 2.5rem;
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
  width: 100%;
  background: var(--color-background);
  border: 2px solid transparent;
  border-radius: 8px;
  padding: 1.5rem;
  text-align: center;
  cursor: pointer;
  font: inherit;
  color: inherit;
  transition: border-color var(--ease-standard), background-color var(--ease-standard);
}

.ratio-card:hover {
  border-color: var(--color-accent);
  background: var(--color-white);
}

.ratio-card:focus-visible {
  outline-offset: 2px;
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
  font-size: 0.9rem;
  color: var(--color-text-light);
}

@media (max-width: 768px) {
  .ratios-grid {
    grid-template-columns: 1fr;
  }
}
</style>