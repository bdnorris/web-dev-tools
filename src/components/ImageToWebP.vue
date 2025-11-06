<template>
  <div class="tool">
    <div class="tool__header">
      <h2 class="tool__title">Image Converter</h2>
      <p class="tool__description">
        Convert JPG and PNG images to modern WebP or AVIF formats for better compression and faster loading times. Reduce file size by up to 90% without noticeable quality loss.
      </p>
    </div>

    <div class="webp-converter">
      <!-- Upload Section -->
      <div class="upload-section">
        <h3 class="section-title">Upload Image</h3>
        <div 
          class="drop-zone"
          :class="{ 'drop-zone--active': isDragging, 'drop-zone--has-image': originalImage }"
          @drop.prevent="handleDrop"
          @dragover.prevent="isDragging = true"
          @dragleave.prevent="isDragging = false"
          @click="triggerFileInput"
        >
          <input 
            ref="fileInput"
            type="file" 
            accept="image/jpeg,image/jpg,image/png"
            @change="handleFileSelect"
            class="file-input"
          />
          <div v-if="!originalImage" class="drop-zone__content">
            <svg class="upload-icon" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M7 16a4 4 0 01-.88-7.903A5 5 0 1115.9 6L16 6a5 5 0 011 9.9M15 13l-3-3m0 0l-3 3m3-3v12" />
            </svg>
            <p class="drop-zone__text">
              <strong>Click to upload</strong> or drag and drop
            </p>
            <p class="drop-zone__hint">JPG or PNG (max 10MB)</p>
          </div>
          <div v-else class="image-preview">
            <img :src="originalImage" alt="Original image" />
            <button @click.stop="clearImage" class="clear-button">×</button>
          </div>
        </div>
      </div>

      <!-- Settings Section -->
      <div v-if="originalImage" class="settings-section">
        <h3 class="section-title">Conversion Settings</h3>
        <div class="settings-grid">
          <div class="input-group">
            <label class="input-label">Output Format</label>
            <div class="format-options">
              <label class="format-option">
                <input 
                  type="radio" 
                  name="format" 
                  value="webp" 
                  v-model="outputFormat"
                  class="format-radio"
                />
                <span class="format-label">
                  <strong>WebP</strong>
                  <span class="format-desc">Excellent compression, wide browser support</span>
                </span>
              </label>
              <label class="format-option">
                <input 
                  type="radio" 
                  name="format" 
                  value="avif" 
                  v-model="outputFormat"
                  class="format-radio"
                />
                <span class="format-label">
                  <strong>AVIF</strong>
                  <span class="format-desc">Superior compression, newer format</span>
                </span>
              </label>
            </div>
          </div>

          <div class="input-group">
            <label for="quality" class="input-label">
              Quality: {{ quality }}%
            </label>
            <input 
              id="quality"
              v-model.number="quality"
              type="range"
              min="0"
              max="100"
              step="5"
              class="slider"
            />
            <div class="slider-labels">
              <span>0%</span>
              <span>50%</span>
              <span>100%</span>
            </div>
          </div>
        </div>

        <button 
          @click="convertImage"
          :disabled="isConverting"
          class="convert-button"
        >
          {{ isConverting ? 'Converting...' : `Convert to ${outputFormat.toUpperCase()}` }}
        </button>
      </div>

      <!-- Results Section -->
      <div v-if="convertedImage" class="results-section">
        <h3 class="section-title">Conversion Results</h3>
        
        <!-- Size Comparison -->
        <div class="comparison-grid">
          <div class="comparison-card">
            <div class="comparison-card__label">Original Size</div>
            <div class="comparison-card__value">{{ formatFileSize(originalSize) }}</div>
            <div class="comparison-card__format">{{ originalFormat.toUpperCase() }}</div>
          </div>
          
          <div class="comparison-arrow">→</div>
          
          <div class="comparison-card comparison-card--highlight">
            <div class="comparison-card__label">{{ outputFormat.toUpperCase() }} Size</div>
            <div class="comparison-card__value">{{ formatFileSize(convertedSize) }}</div>
            <div class="comparison-card__savings">
              <span v-if="savings > 0" class="savings-positive">
                ↓ {{ savings }}% smaller
              </span>
              <span v-else class="savings-negative">
                ↑ {{ Math.abs(savings) }}% larger
              </span>
            </div>
          </div>
        </div>

        <!-- Preview -->
        <div class="preview-section">
          <h4 class="preview-title">{{ outputFormat.toUpperCase() }} Preview</h4>
          <div class="preview-image-container">
            <img :src="convertedImage" :alt="`Converted ${outputFormat.toUpperCase()} image`" class="preview-image" />
          </div>
        </div>

        <!-- Download Button -->
        <button @click="downloadImage" class="download-button">
          <svg class="download-icon" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 16v1a3 3 0 003 3h10a3 3 0 003-3v-1m-4-4l-4 4m0 0l-4-4m4 4V4" />
          </svg>
          Download {{ outputFormat.toUpperCase() }}
        </button>

        <!-- Try Another -->
        <button @click="reset" class="reset-button">
          Convert Another Image
        </button>
      </div>

      <!-- Error Display -->
      <div v-if="error" class="error-message">
        <strong>Error:</strong> {{ error }}
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { encode as encodeWebP } from '@jsquash/webp'
import { encode as encodeAvif } from '@jsquash/avif'

const fileInput = ref(null)
const isDragging = ref(false)
const originalImage = ref(null)
const convertedImage = ref(null)
const originalSize = ref(0)
const convertedSize = ref(0)
const originalFormat = ref('')
const outputFormat = ref('webp')
const quality = ref(80)
const isConverting = ref(false)
const error = ref('')
const fileName = ref('')

const triggerFileInput = () => {
  fileInput.value?.click()
}

const handleFileSelect = (event) => {
  const file = event.target.files?.[0]
  if (file) {
    processFile(file)
  }
}

const handleDrop = (event) => {
  isDragging.value = false
  const file = event.dataTransfer?.files?.[0]
  if (file) {
    processFile(file)
  }
}

const processFile = (file) => {
  error.value = ''
  
  // Validate file type
  if (!['image/jpeg', 'image/jpg', 'image/png'].includes(file.type)) {
    error.value = 'Please upload a JPG or PNG image.'
    return
  }
  
  // Validate file size (10MB max)
  if (file.size > 10 * 1024 * 1024) {
    error.value = 'File size must be less than 10MB.'
    return
  }
  
  originalSize.value = file.size
  originalFormat.value = file.type.split('/')[1]
  fileName.value = file.name.replace(/\.[^/.]+$/, '')
  
  const reader = new FileReader()
  reader.onload = (e) => {
    originalImage.value = e.target.result
    convertedImage.value = null // Reset converted image
  }
  reader.readAsDataURL(file)
}

const clearImage = () => {
  originalImage.value = null
  convertedImage.value = null
  originalSize.value = 0
  convertedSize.value = 0
  error.value = ''
  if (fileInput.value) {
    fileInput.value.value = ''
  }
}

const convertImage = async () => {
  if (!originalImage.value) return
  
  isConverting.value = true
  error.value = ''
  
  try {
    // Create image element to get pixel data
    const img = new Image()
    img.src = originalImage.value
    
    await new Promise((resolve, reject) => {
      img.onload = resolve
      img.onerror = reject
    })
    
    // Create canvas and get image data
    const canvas = document.createElement('canvas')
    canvas.width = img.width
    canvas.height = img.height
    const ctx = canvas.getContext('2d')
    ctx.drawImage(img, 0, 0)
    const imageData = ctx.getImageData(0, 0, canvas.width, canvas.height)
    
    // Convert to selected format
    let convertedData
    let mimeType
    
    if (outputFormat.value === 'webp') {
      convertedData = await encodeWebP(imageData, {
        quality: quality.value
      })
      mimeType = 'image/webp'
    } else if (outputFormat.value === 'avif') {
      convertedData = await encodeAvif(imageData, {
        quality: quality.value
      })
      mimeType = 'image/avif'
    }
    
    // Create blob and data URL
    const blob = new Blob([convertedData], { type: mimeType })
    convertedSize.value = blob.size
    convertedImage.value = URL.createObjectURL(blob)
    
    // Calculate savings
    updateSavings()
    
  } catch (err) {
    console.error('Conversion error:', err)
    error.value = `Conversion failed: ${err.message}`
  } finally {
    isConverting.value = false
  }
}

const downloadImage = () => {
  if (!convertedImage.value) return
  
  const link = document.createElement('a')
  link.href = convertedImage.value
  link.download = `${fileName.value}.${outputFormat.value}`
  link.click()
}

const reset = () => {
  clearImage()
}

const formatFileSize = (bytes) => {
  if (bytes === 0) return '0 Bytes'
  const k = 1024
  const sizes = ['Bytes', 'KB', 'MB']
  const i = Math.floor(Math.log(bytes) / Math.log(k))
  return Math.round((bytes / Math.pow(k, i)) * 100) / 100 + ' ' + sizes[i]
}

const savings = ref(0)

// Update savings calculation whenever sizes change
const updateSavings = () => {
  if (originalSize.value && convertedSize.value) {
    savings.value = Math.round(((originalSize.value - convertedSize.value) / originalSize.value) * 100)
  }
}
</script>

<style scoped>
.webp-converter {
  display: flex;
  flex-direction: column;
  gap: 2rem;
}

.drop-zone {
  border: 2px dashed #e1e5e9;
  border-radius: 12px;
  padding: 3rem 2rem;
  text-align: center;
  cursor: pointer;
  transition: all 0.3s ease;
  background: #f8f9fa;
  position: relative;
}

.drop-zone:hover {
  border-color: var(--color-accent);
  background: #fff;
}

.drop-zone--active {
  border-color: var(--color-accent);
  background: var(--color-accent-light);
  opacity: 0.8;
}

.drop-zone--has-image {
  padding: 0;
  border: none;
  background: transparent;
}

.file-input {
  display: none;
}

.drop-zone__content {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 1rem;
}

.upload-icon {
  width: 64px;
  height: 64px;
  color: var(--color-accent);
}

.drop-zone__text {
  margin: 0;
  font-size: 1.1rem;
  color: var(--color-text);
}

.drop-zone__hint {
  margin: 0;
  font-size: 0.9rem;
  color: var(--color-text-light);
}

.image-preview {
  position: relative;
  max-width: 100%;
  border-radius: 12px;
  overflow: hidden;
}

.image-preview img {
  width: 100%;
  height: auto;
  display: block;
  max-height: 400px;
  object-fit: contain;
}

.clear-button {
  position: absolute;
  top: 1rem;
  right: 1rem;
  width: 40px;
  height: 40px;
  border-radius: 50%;
  background: var(--color-secondary);
  color: white;
  border: none;
  font-size: 1.5rem;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.3s ease;
  line-height: 1;
}

.clear-button:hover {
  background: var(--color-primary);
  transform: scale(1.1);
}

.settings-grid {
  display: flex;
  flex-direction: column;
  gap: 1.5rem;
}

.format-options {
  display: flex;
  flex-direction: column;
  gap: 0.75rem;
}

.format-option {
  display: flex;
  align-items: flex-start;
  gap: 0.75rem;
  padding: 1rem;
  border: 2px solid #e1e5e9;
  border-radius: 8px;
  cursor: pointer;
  transition: all 0.3s ease;
}

.format-option:hover {
  border-color: var(--color-accent);
  background: #f8f9fa;
}

.format-option:has(.format-radio:checked) {
  border-color: var(--color-accent);
  background: var(--color-accent-light);
}

.format-radio {
  margin-top: 0.25rem;
  width: 18px;
  height: 18px;
  cursor: pointer;
  accent-color: var(--color-accent);
}

.format-label {
  display: flex;
  flex-direction: column;
  gap: 0.25rem;
  flex: 1;
}

.format-label strong {
  color: var(--color-primary);
  font-size: 1rem;
}

.format-desc {
  font-size: 0.85rem;
  color: var(--color-text-light);
}

.slider {
  width: 100%;
  height: 8px;
  border-radius: 4px;
  background: #e1e5e9;
  outline: none;
  -webkit-appearance: none;
  appearance: none;
}

.slider::-webkit-slider-thumb {
  -webkit-appearance: none;
  appearance: none;
  width: 20px;
  height: 20px;
  border-radius: 50%;
  background: var(--color-accent);
  cursor: pointer;
  transition: all 0.3s ease;
}

.slider::-webkit-slider-thumb:hover {
  background: var(--color-secondary);
  transform: scale(1.2);
}

.slider::-moz-range-thumb {
  width: 20px;
  height: 20px;
  border-radius: 50%;
  background: var(--color-accent);
  cursor: pointer;
  border: none;
  transition: all 0.3s ease;
}

.slider::-moz-range-thumb:hover {
  background: var(--color-secondary);
  transform: scale(1.2);
}

.slider-labels {
  display: flex;
  justify-content: space-between;
  font-size: 0.85rem;
  color: var(--color-text-light);
  margin-top: 0.25rem;
}

.convert-button {
  width: 100%;
  padding: 1rem 2rem;
  background: var(--color-accent);
  color: white;
  border: none;
  border-radius: 8px;
  font-size: 1.1rem;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;
  margin-top: 1rem;
}

.convert-button:hover:not(:disabled) {
  background: var(--color-secondary);
  transform: translateY(-2px);
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
}

.convert-button:disabled {
  background: #ccc;
  cursor: not-allowed;
}

.comparison-grid {
  display: grid;
  grid-template-columns: 1fr auto 1fr;
  gap: 1.5rem;
  align-items: center;
  margin-bottom: 2rem;
}

.comparison-card {
  background: #f8f9fa;
  border-radius: 12px;
  padding: 1.5rem;
  text-align: center;
  border: 2px solid #e1e5e9;
}

.comparison-card--highlight {
  background: linear-gradient(135deg, var(--color-accent-light), var(--color-accent));
  border-color: var(--color-accent);
  color: var(--color-primary);
}

.comparison-card__label {
  font-size: 0.9rem;
  color: var(--color-text-light);
  margin-bottom: 0.5rem;
}

.comparison-card--highlight .comparison-card__label {
  color: var(--color-primary);
  font-weight: 500;
}

.comparison-card__value {
  font-size: 1.75rem;
  font-weight: 700;
  margin-bottom: 0.25rem;
}

.comparison-card__format {
  font-size: 0.85rem;
  color: var(--color-text-light);
  text-transform: uppercase;
  font-weight: 600;
}

.comparison-card__savings {
  margin-top: 0.5rem;
  font-weight: 600;
  font-size: 1rem;
}

.savings-positive {
  color: #28a745;
}

.savings-negative {
  color: #dc3545;
}

.comparison-arrow {
  font-size: 2rem;
  color: var(--color-accent);
  font-weight: bold;
}

.preview-section {
  margin: 2rem 0;
}

.preview-title {
  margin: 0 0 1rem 0;
  color: var(--color-primary);
  font-size: 1.1rem;
}

.preview-image-container {
  border: 1px solid #e1e5e9;
  border-radius: 12px;
  overflow: hidden;
  background: #f8f9fa;
  padding: 1rem;
}

.preview-image {
  width: 100%;
  height: auto;
  max-height: 400px;
  object-fit: contain;
  display: block;
  margin: 0 auto;
}

.download-button {
  width: 100%;
  padding: 1rem 2rem;
  background: var(--color-primary);
  color: white;
  border: none;
  border-radius: 8px;
  font-size: 1.1rem;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 0.5rem;
  margin-bottom: 1rem;
}

.download-button:hover {
  background: var(--color-secondary);
  transform: translateY(-2px);
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
}

.download-icon {
  width: 24px;
  height: 24px;
}

.reset-button {
  width: 100%;
  padding: 0.75rem 1.5rem;
  background: transparent;
  color: var(--color-primary);
  border: 2px solid var(--color-primary);
  border-radius: 8px;
  font-size: 1rem;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;
}

.reset-button:hover {
  background: var(--color-primary);
  color: white;
}

.error-message {
  padding: 1rem;
  background: #f8d7da;
  border: 1px solid #f5c6cb;
  border-radius: 8px;
  color: #721c24;
}

@media (max-width: 768px) {
  .comparison-grid {
    grid-template-columns: 1fr;
    gap: 1rem;
  }
  
  .comparison-arrow {
    transform: rotate(90deg);
  }
  
  .drop-zone {
    padding: 2rem 1rem;
  }
  
  .upload-icon {
    width: 48px;
    height: 48px;
  }
}
</style>

