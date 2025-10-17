<template>
  <div class="tool">
    <div class="tool__header">
      <h2 class="tool__title">Pixel to Em Converter</h2>
      <p class="tool__description">
        Convert pixels to em units based on your base font size. Perfect for responsive typography and spacing.
      </p>
    </div>

    <div class="pixel-em-converter">
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
              class="input"
              min="1"
              @input="convertValues"
            />
            <span class="unit">px</span>
          </div>
        </div>
      </div>

      <!-- Conversion Section -->
      <div class="conversion-section">
        <h3 class="section-title">Convert Values</h3>
        <div class="converter-grid">
          <div class="input-group">
            <label for="pixels" class="input-label">Pixels</label>
            <div class="input-with-unit">
              <input 
                id="pixels"
                v-model.number="pixels" 
                type="number" 
                class="input input--large"
                placeholder="16"
                @input="convertToEm"
              />
              <span class="unit">px</span>
            </div>
          </div>

          <div class="conversion-equals">=</div>

          <div class="input-group">
            <label for="ems" class="input-label">Ems</label>
            <div class="input-with-unit">
              <input 
                id="ems"
                v-model.number="ems" 
                type="number" 
                class="input input--large"
                placeholder="1"
                step="0.01"
                @input="convertToPixels"
              />
              <span class="unit">em</span>
            </div>
          </div>
        </div>

        <!-- Calculation Display -->
        <div v-if="pixels && baseSize" class="calculation-display">
          <div class="calculation">
            <span class="calculation__formula">
              {{ pixels }}px ÷ {{ baseSize }}px = {{ (pixels / baseSize).toFixed(4) }}em
            </span>
          </div>
        </div>
      </div>

      <!-- Common Values Table -->
      <div class="common-values-section">
        <h3 class="section-title">Common Values</h3>
        <div class="values-table">
          <div class="table-header">
            <div class="table-cell">Pixels</div>
            <div class="table-cell">Em ({{ baseSize }}px base)</div>
            <div class="table-cell">Usage</div>
          </div>
          <div 
            v-for="value in commonValues" 
            :key="value.px" 
            class="table-row"
            @click="setValues(value.px)"
          >
            <div class="table-cell">{{ value.px }}px</div>
            <div class="table-cell">{{ (value.px / baseSize).toFixed(3) }}em</div>
            <div class="table-cell table-cell--usage">{{ value.usage }}</div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'

const baseSize = ref(16)
const pixels = ref('')
const ems = ref('')

const commonValues = [
  { px: 8, usage: 'Small spacing' },
  { px: 12, usage: 'Small text' },
  { px: 14, usage: 'Body text (small)' },
  { px: 16, usage: 'Body text (default)' },
  { px: 18, usage: 'Large body text' },
  { px: 20, usage: 'Small heading' },
  { px: 24, usage: 'Medium heading' },
  { px: 32, usage: 'Large heading' },
  { px: 48, usage: 'Hero text' },
  { px: 64, usage: 'Display text' }
]

const convertToEm = () => {
  if (pixels.value && baseSize.value) {
    ems.value = Math.round((pixels.value / baseSize.value) * 10000) / 10000
  } else {
    ems.value = ''
  }
}

const convertToPixels = () => {
  if (ems.value && baseSize.value) {
    pixels.value = Math.round(ems.value * baseSize.value * 100) / 100
  } else {
    pixels.value = ''
  }
}

const convertValues = () => {
  if (pixels.value) {
    convertToEm()
  } else if (ems.value) {
    convertToPixels()
  }
}

const setValues = (pixelValue) => {
  pixels.value = pixelValue
  convertToEm()
}

onMounted(() => {
  // Set default values
  pixels.value = 16
  convertToEm()
})
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

.pixel-em-converter {
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
  width: 150px;
  transition: all 0.3s ease;
}

.input--large {
  width: 200px;
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

.converter-grid {
  display: flex;
  align-items: end;
  gap: 2rem;
  flex-wrap: wrap;
}

.conversion-equals {
  font-size: 2rem;
  color: var(--color-accent);
  font-weight: bold;
  margin-bottom: 0.5rem;
}

.calculation-display {
  margin-top: 1.5rem;
  padding: 1rem;
  background: var(--color-background);
  border-radius: 8px;
  border-left: 4px solid var(--color-accent);
}

.calculation__formula {
  font-family: 'Monaco', 'Menlo', 'Ubuntu Mono', monospace;
  font-size: 1rem;
  color: var(--color-primary);
  font-weight: 600;
}

.values-table {
  border: 1px solid #e1e5e9;
  border-radius: 8px;
  overflow: hidden;
}

.table-header,
.table-row {
  display: grid;
  grid-template-columns: 1fr 1fr 2fr;
}

.table-header {
  background: var(--color-primary);
  color: var(--color-white);
  font-weight: 600;
}

.table-row {
  border-top: 1px solid #e1e5e9;
  cursor: pointer;
  transition: background-color 0.2s ease;
}

.table-row:hover {
  background: var(--color-background);
}

.table-cell {
  padding: 0.75rem;
}

.table-cell--usage {
  color: var(--color-text-light);
  font-size: 0.9rem;
}

@media (max-width: 768px) {
  .tool {
    padding: 1.5rem;
  }
  
  .converter-grid {
    justify-content: center;
    flex-direction: column;
    align-items: center;
  }
  
  .conversion-equals {
    transform: rotate(90deg);
  }
  
  .input,
  .input--large {
    width: 100%;
    max-width: 200px;
  }
  
  .values-table {
    font-size: 0.9rem;
  }
  
  .table-header,
  .table-row {
    grid-template-columns: 80px 100px 1fr;
  }
  
  .table-cell {
    padding: 0.5rem;
  }
}
</style>