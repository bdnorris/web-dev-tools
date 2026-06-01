<template>
  <div class="tool">
    <div class="tool__header">
      <h2 class="tool__title">Text Compare</h2>
      <p class="tool__description">
        Paste text into both fields to see a line-by-line diff of the differences.
      </p>
    </div>

    <div class="text-compare">
      <div class="compare-grid">
        <div class="textarea-group">
          <div class="textarea-header">
            <label for="text-a" class="input-label">Original</label>
            <button
              v-if="textA"
              type="button"
              class="clear-button"
              @click="textA = ''"
            >
              Clear
            </button>
          </div>
          <textarea
            id="text-a"
            v-model="textA"
            class="compare-textarea"
            placeholder="Paste original text here..."
            spellcheck="false"
          ></textarea>
        </div>

        <div class="textarea-group">
          <div class="textarea-header">
            <label for="text-b" class="input-label">Modified</label>
            <button
              v-if="textB"
              type="button"
              class="clear-button"
              @click="textB = ''"
            >
              Clear
            </button>
          </div>
          <textarea
            id="text-b"
            v-model="textB"
            class="compare-textarea"
            placeholder="Paste modified text here..."
            spellcheck="false"
          ></textarea>
        </div>
      </div>

      <div class="diff-section">
        <h3 class="section-title">Differences</h3>

        <p v-if="isEmpty" class="diff-placeholder">
          Paste text above to compare.
        </p>

        <template v-else-if="isIdentical">
          <p class="diff-summary diff-summary--neutral">No differences</p>
        </template>

        <template v-else>
          <p class="diff-summary">
            <span v-if="linesRemoved">{{ linesRemoved }} line{{ linesRemoved === 1 ? '' : 's' }} removed</span>
            <span v-if="linesRemoved && linesAdded">, </span>
            <span v-if="linesAdded">{{ linesAdded }} line{{ linesAdded === 1 ? '' : 's' }} added</span>
          </p>

          <div class="diff-panel">
            <div
              v-for="(part, index) in diffParts"
              :key="index"
              :class="[
                'diff-chunk',
                {
                  'diff-chunk--added': part.added,
                  'diff-chunk--removed': part.removed,
                },
              ]"
            >
              <span class="diff-prefix">{{ part.added ? '+' : part.removed ? '-' : ' ' }}</span>
              <pre class="diff-content">{{ part.value }}</pre>
            </div>
          </div>
        </template>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'
import { diffLines } from 'diff'

const textA = ref('')
const textB = ref('')

const diffParts = computed(() =>
  diffLines(textA.value || '', textB.value || '')
)

const isEmpty = computed(() => !textA.value && !textB.value)

const isIdentical = computed(() => {
  if (isEmpty.value) return false
  return diffParts.value.every((part) => !part.added && !part.removed)
})

const countLines = (value) => {
  if (!value) return 0
  const lines = value.split('\n')
  if (lines[lines.length - 1] === '') {
    return lines.length - 1
  }
  return lines.length
}

const linesRemoved = computed(() =>
  diffParts.value
    .filter((part) => part.removed)
    .reduce((sum, part) => sum + countLines(part.value), 0)
)

const linesAdded = computed(() =>
  diffParts.value
    .filter((part) => part.added)
    .reduce((sum, part) => sum + countLines(part.value), 0)
)
</script>

<style scoped>
.text-compare {
  display: flex;
  flex-direction: column;
  gap: 2rem;
}

.compare-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 1.5rem;
}

.textarea-group {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
}

.textarea-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 0.5rem;
}

.clear-button {
  background: none;
  border: none;
  color: var(--color-text-light);
  font-size: 0.85rem;
  cursor: pointer;
  padding: 0.25rem 0.5rem;
  border-radius: 4px;
  transition: color 0.2s ease, background-color 0.2s ease;
}

.clear-button:hover {
  color: var(--color-secondary);
  background: rgba(214, 40, 40, 0.08);
}

.compare-textarea {
  width: 100%;
  min-height: 200px;
  padding: 1rem;
  border: 2px solid #e1e5e9;
  border-radius: 8px;
  font-family: 'IBM Plex Mono', 'Monaco', 'Menlo', 'Ubuntu Mono', monospace;
  font-size: 0.9rem;
  line-height: 1.5;
  resize: vertical;
  transition: all 0.3s ease;
}

.compare-textarea:focus {
  outline: none;
  border-color: var(--color-accent);
  box-shadow: 0 0 0 3px rgba(247, 127, 0, 0.1);
}

.compare-textarea::placeholder {
  color: var(--color-text-light);
  opacity: 0.7;
}

.diff-section {
  display: flex;
  flex-direction: column;
  gap: 0.75rem;
}

.diff-placeholder,
.diff-summary--neutral {
  color: var(--color-text-light);
  margin: 0;
  font-size: 0.95rem;
}

.diff-summary {
  margin: 0;
  font-size: 0.95rem;
  color: var(--color-text);
  font-weight: 500;
}

.diff-panel {
  max-height: 480px;
  overflow: auto;
  border: 1px solid #e1e5e9;
  border-radius: 8px;
  font-family: 'IBM Plex Mono', 'Monaco', 'Menlo', 'Ubuntu Mono', monospace;
  font-size: 0.85rem;
  line-height: 1.5;
}

.diff-chunk {
  display: flex;
  align-items: flex-start;
  padding: 0 0.5rem;
  background: #f8f9fa;
  border-bottom: 1px solid #eef1f4;
}

.diff-chunk:last-child {
  border-bottom: none;
}

.diff-chunk--removed {
  background: #fde8e8;
}

.diff-chunk--added {
  background: #e6f4ea;
}

.diff-prefix {
  flex-shrink: 0;
  width: 1.25rem;
  padding: 0.125rem 0;
  color: var(--color-text-light);
  user-select: none;
}

.diff-chunk--removed .diff-prefix {
  color: var(--color-secondary);
  font-weight: 600;
}

.diff-chunk--added .diff-prefix {
  color: #1e7e34;
  font-weight: 600;
}

.diff-content {
  margin: 0;
  padding: 0.125rem 0;
  white-space: pre-wrap;
  word-break: break-word;
  flex: 1;
}

@media (max-width: 768px) {
  .compare-grid {
    grid-template-columns: 1fr;
  }

  .compare-textarea {
    min-height: 150px;
    font-size: 0.8rem;
  }

  .diff-panel {
    max-height: 360px;
    font-size: 0.8rem;
  }
}
</style>
