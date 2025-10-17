<template>
  <div class="tool">
    <div class="tool__header">
      <h2 class="tool__title">Ratio Converter</h2>
      <p class="tool__description">
        Enter a ratio and convert values proportionally. Input any value on either side to see the proportional result.
      </p>
    </div>

    <div class="ratio-converter">
      <!-- Ratio Input -->
      <div class="ratio-section">
        <h3 class="section-title">Set Your Ratio</h3>
        <div class="ratio-inputs">
          <div class="input-group">
            <label for="ratio-a" class="input-label">A</label>
            <input 
              id="ratio-a"
              v-model.number="ratioA" 
              type="number" 
              class="input input--small"
              placeholder="3"
              @input="calculateFromRatio"
            />
          </div>
          <div class="ratio-separator">:</div>
          <div class="input-group">
            <label for="ratio-b" class="input-label">B</label>
            <input 
              id="ratio-b"
              v-model.number="ratioB" 
              type="number" 
              class="input input--small"
              placeholder="4"
              @input="calculateFromRatio"
            />
          </div>
        </div>
      </div>

      <!-- Conversion Section -->
      <div class="conversion-section">
        <h3 class="section-title">Convert Values</h3>
        <div class="conversion-inputs">
          <div class="input-group">
            <label for="value-1" class="input-label">Value 1</label>
            <input 
              id="value-1"
              v-model.number="value1" 
              type="number" 
              class="input input--large"
              placeholder="Enter value"
              @input="calculateValue2"
            />
          </div>
          <div class="conversion-arrow">→</div>
          <div class="input-group">
            <label for="value-2" class="input-label">Value 2</label>
            <input 
              id="value-2"
              v-model.number="value2" 
              type="number" 
              class="input input--large"
              placeholder="Enter value"
              @input="calculateValue1"
            />
          </div>
        </div>

        <!-- Ratio Display -->
        <div v-if="hasValidRatio" class="calculation-display">
          <span class="calculation__formula">
            Ratio: {{ ratioA }}:{{ ratioB }}
          </span>
          <span class="ratio-decimal">
            ({{ ratioDecimal }})
          </span>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

const ratioA = ref(3)
const ratioB = ref(4)

const value1 = ref('')
const value2 = ref('')

const hasValidRatio = computed(() => {
  return ratioA.value > 0 && ratioB.value > 0
})

const ratioDecimal = computed(() => {
  if (!hasValidRatio.value) return ''
  return `${(ratioA.value / ratioB.value).toFixed(3)}:1`
})

const calculateValue2 = () => {
  if (!value1.value || !hasValidRatio.value) {
    value2.value = ''
    return
  }
  
  const proportion = ratioB.value / ratioA.value
  value2.value = Math.round((value1.value * proportion) * 1000) / 1000
}

const calculateValue1 = () => {
  if (!value2.value || !hasValidRatio.value) {
    value1.value = ''
    return
  }
  
  const proportion = ratioA.value / ratioB.value
  value1.value = Math.round((value2.value * proportion) * 1000) / 1000
}

const calculateFromRatio = () => {
  if (value1.value) {
    calculateValue2()
  } else if (value2.value) {
    calculateValue1()
  }
}
</script>

<style scoped>
.ratio-converter {
  display: flex;
  flex-direction: column;
  gap: 2.5rem;
}

.ratio-inputs {
  display: flex;
  align-items: end;
  gap: 1rem;
  flex-wrap: wrap;
}

.ratio-separator {
  font-size: 1.5rem;
  font-weight: 600;
  color: var(--color-text-light);
  margin-bottom: 0.5rem;
}

.conversion-inputs {
  display: flex;
  align-items: end;
  gap: 2rem;
  flex-wrap: wrap;
}

.conversion-arrow {
  font-size: 2rem;
  color: var(--color-accent);
  font-weight: bold;
  margin-bottom: 0.5rem;
}

.calculation-display {
  margin-top: 1rem;
}

.ratio-decimal {
  display: block;
  font-size: 0.9rem;
  color: var(--color-text-light);
  margin-top: 0.25rem;
}

@media (max-width: 768px) {
  .ratio-inputs {
    justify-content: center;
  }
  
  .conversion-inputs {
    justify-content: center;
    flex-direction: column;
    align-items: center;
  }
  
  .conversion-arrow {
    transform: rotate(90deg);
  }
}
</style>