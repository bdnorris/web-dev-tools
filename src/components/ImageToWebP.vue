<template>
  <div class="tool">
    <div class="tool__header">
      <h2 class="tool__title">Image Converter</h2>
      <p class="tool__description">
        Convert and compress JPG and PNG images to multiple formats. Upload an image to automatically generate WebP, AVIF, PNG, and JPG compressed versions with file size comparisons. Recommended maximum: 5000×5000 pixels (25MP).
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
            <p class="drop-zone__hint">JPG or PNG (max 10MB, recommended: 5000×5000px)</p>
          </div>
          <div v-else class="image-preview">
            <img :src="originalImage" alt="Original image" />
            <button @click.stop="clearImage" class="clear-button">×</button>
          </div>
        </div>
      </div>

      <!-- Quality Settings -->
      <div v-if="originalImage && !isProcessing" class="settings-section">
        <h3 class="section-title">Compression Quality</h3>
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
            @input="reprocessAllFormats"
          />
          <div class="slider-labels">
            <span>0%</span>
            <span>50%</span>
            <span>100%</span>
          </div>
          <p class="quality-hint">Adjust quality to balance file size and image quality</p>
        </div>
      </div>

      <!-- Loading Indicator -->
      <div v-if="isProcessing" class="loading-section">
        <div class="loading-spinner"></div>
        <p class="loading-text">{{ processingStatus || 'Processing all formats...' }}</p>
        <p v-if="processingStatus" class="loading-hint">This may take a moment for large images...</p>
      </div>

      <!-- Results Section -->
      <div v-if="results && !isProcessing" class="results-section">
        <h3 class="section-title">Compression Results</h3>
        
        <!-- Format Cards Grid -->
        <div class="formats-grid">
          <!-- Original Card -->
          <div class="format-card">
            <div class="format-card__header">
              <div class="format-card__label">Original</div>
              <div class="format-card__format">{{ originalFormat.toUpperCase() }}</div>
            </div>
            <div class="format-card__size">{{ formatFileSize(results.original.size) }}</div>
            <div class="format-card__savings">Reference</div>
            <button @click="downloadFormat('original')" class="format-card__download" :disabled="!results.original.url">
              <svg class="download-icon" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 16v1a3 3 0 003 3h10a3 3 0 003-3v-1m-4-4l-4 4m0 0l-4-4m4 4V4" />
              </svg>
              Download
            </button>
          </div>

          <!-- WebP Card -->
          <div class="format-card" :class="{ 'format-card--best': bestFormat === 'webp' }">
            <div class="format-card__header">
              <div class="format-card__label">WebP</div>
              <div class="format-card__format">WEBP</div>
            </div>
            <div class="format-card__size">{{ formatFileSize(results.webp.size) }}</div>
            <div class="format-card__savings" :class="getSavingsClass(results.webp.savings)">
              <span v-if="results.webp.savings > 0">↓ {{ results.webp.savings }}% smaller</span>
              <span v-else-if="results.webp.savings < 0">↑ {{ Math.abs(results.webp.savings) }}% larger</span>
              <span v-else>Same size</span>
            </div>
            <button @click="downloadFormat('webp')" class="format-card__download" :disabled="!results.webp.url">
              <svg class="download-icon" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 16v1a3 3 0 003 3h10a3 3 0 003-3v-1m-4-4l-4 4m0 0l-4-4m4 4V4" />
              </svg>
              Download
            </button>
          </div>

          <!-- PNG Card -->
          <div class="format-card" :class="{ 'format-card--best': bestFormat === 'png' }">
            <div class="format-card__header">
              <div class="format-card__label">PNG</div>
              <div class="format-card__format">PNG</div>
            </div>
            <div class="format-card__size">{{ formatFileSize(results.png.size) }}</div>
            <div class="format-card__savings" :class="getSavingsClass(results.png.savings)">
              <span v-if="results.png.savings > 0">↓ {{ results.png.savings }}% smaller</span>
              <span v-else-if="results.png.savings < 0">↑ {{ Math.abs(results.png.savings) }}% larger</span>
              <span v-else>Same size</span>
            </div>
            <button @click="downloadFormat('png')" class="format-card__download" :disabled="!results.png.url">
              <svg class="download-icon" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 16v1a3 3 0 003 3h10a3 3 0 003-3v-1m-4-4l-4 4m0 0l-4-4m4 4V4" />
              </svg>
              Download
            </button>
          </div>

          <!-- AVIF Card -->
          <div class="format-card" :class="{ 'format-card--best': bestFormat === 'avif' }">
            <div class="format-card__header">
              <div class="format-card__label">AVIF</div>
              <div class="format-card__format">AVIF</div>
            </div>
            <div class="format-card__size">{{ formatFileSize(results.avif.size) }}</div>
            <div class="format-card__savings" :class="getSavingsClass(results.avif.savings)">
              <span v-if="results.avif.savings > 0">↓ {{ results.avif.savings }}% smaller</span>
              <span v-else-if="results.avif.savings < 0">↑ {{ Math.abs(results.avif.savings) }}% larger</span>
              <span v-else>Same size</span>
            </div>
            <button @click="downloadFormat('avif')" class="format-card__download" :disabled="!results.avif.url">
              <svg class="download-icon" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 16v1a3 3 0 003 3h10a3 3 0 003-3v-1m-4-4l-4 4m0 0l-4-4m4 4V4" />
              </svg>
              Download
            </button>
          </div>

          <!-- JPG Card -->
          <div class="format-card" :class="{ 'format-card--best': bestFormat === 'jpg' }">
            <div class="format-card__header">
              <div class="format-card__label">JPG</div>
              <div class="format-card__format">JPG</div>
            </div>
            <div class="format-card__size">{{ formatFileSize(results.jpg.size) }}</div>
            <div class="format-card__savings" :class="getSavingsClass(results.jpg.savings)">
              <span v-if="results.jpg.savings > 0">↓ {{ results.jpg.savings }}% smaller</span>
              <span v-else-if="results.jpg.savings < 0">↑ {{ Math.abs(results.jpg.savings) }}% larger</span>
              <span v-else>Same size</span>
            </div>
            <button @click="downloadFormat('jpg')" class="format-card__download" :disabled="!results.jpg.url">
              <svg class="download-icon" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 16v1a3 3 0 003 3h10a3 3 0 003-3v-1m-4-4l-4 4m0 0l-4-4m4 4V4" />
              </svg>
              Download
            </button>
          </div>
        </div>

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
import { ref, computed, watch } from 'vue'
import { encode as encodeWebP } from '@jsquash/webp'
import { encode as encodeAvif } from '@jsquash/avif'
import { encode as encodeJpeg } from '@jsquash/jpeg'
import { optimise as optimisePng } from '@jsquash/oxipng'

const fileInput = ref(null)
const isDragging = ref(false)
const originalImage = ref(null)
const originalSize = ref(0)
const originalFormat = ref('')
const quality = ref(80)
const isProcessing = ref(false)
const error = ref('')
const fileName = ref('')
const processingStatus = ref('')
const maxPixels = 25_000_000 // ~25MP target (5000×5000) - will downscale to this
const maxPixelsHardLimit = 50_000_000 // ~50MP hard limit - reject if over this
const maxPixelsWarning = 15_000_000 // ~15MP warning threshold

// Results object storing all 4 formats
const results = ref(null)

// Best format (smallest file size)
const bestFormat = computed(() => {
  if (!results.value) return null
  
  const formats = ['webp', 'avif', 'png', 'jpg']
  let best = 'webp'
  let smallest = results.value.webp.size
  
  formats.forEach(format => {
    if (results.value[format].size < smallest) {
      smallest = results.value[format].size
      best = format
    }
  })
  
  return best
})

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
  reader.onload = async (e) => {
    originalImage.value = e.target.result
    results.value = null
    // Automatically process all formats
    await processAllFormats(e.target.result, file)
  }
  reader.readAsDataURL(file)
}

const clearImage = () => {
  // Clean up object URLs
  if (results.value) {
    if (results.value.webp?.url && results.value.webp.url.startsWith('blob:')) {
      URL.revokeObjectURL(results.value.webp.url)
    }
    if (results.value.avif?.url && results.value.avif.url.startsWith('blob:')) {
      URL.revokeObjectURL(results.value.avif.url)
    }
    if (results.value.png?.url && results.value.png.url.startsWith('blob:')) {
      URL.revokeObjectURL(results.value.png.url)
    }
    if (results.value.jpg?.url && results.value.jpg.url.startsWith('blob:')) {
      URL.revokeObjectURL(results.value.jpg.url)
    }
  }
  
  originalImage.value = null
  results.value = null
  originalSize.value = 0
  error.value = ''
  if (fileInput.value) {
    fileInput.value.value = ''
  }
}

// Check available memory
const checkMemory = () => {
  if (performance.memory) {
    const usedMB = performance.memory.usedJSHeapSize / 1048576
    const limitMB = performance.memory.jsHeapSizeLimit / 1048576
    const availableMB = limitMB - usedMB
    return { usedMB, limitMB, availableMB }
  }
  return null
}

// Downscale image if too large
const downscaleImage = (img, maxPixels) => {
  const currentPixels = img.width * img.height
  if (currentPixels <= maxPixels) {
    return { width: img.width, height: img.height, scale: 1 }
  }
  
  const scale = Math.sqrt(maxPixels / currentPixels)
  return {
    width: Math.floor(img.width * scale),
    height: Math.floor(img.height * scale),
    scale: scale
  }
}

const processAllFormats = async (imageDataUrl, originalFile) => {
  if (!imageDataUrl) return
  
  // Clean up previous object URLs
  if (results.value) {
    if (results.value.webp?.url && results.value.webp.url.startsWith('blob:')) {
      URL.revokeObjectURL(results.value.webp.url)
    }
    if (results.value.avif?.url && results.value.avif.url.startsWith('blob:')) {
      URL.revokeObjectURL(results.value.avif.url)
    }
    if (results.value.png?.url && results.value.png.url.startsWith('blob:')) {
      URL.revokeObjectURL(results.value.png.url)
    }
    if (results.value.jpg?.url && results.value.jpg.url.startsWith('blob:')) {
      URL.revokeObjectURL(results.value.jpg.url)
    }
  }
  
  isProcessing.value = true
  error.value = ''
  processingStatus.value = 'Loading image...'
  
  try {
    // Check memory before starting
    const memoryInfo = checkMemory()
    if (memoryInfo && memoryInfo.availableMB < 200) {
      console.warn('Low memory available:', memoryInfo.availableMB, 'MB')
    }
    
    // Create image element to get pixel data
    const img = new Image()
    img.src = imageDataUrl
    
    await new Promise((resolve, reject) => {
      img.onload = resolve
      img.onerror = reject
    })
    
    // Check image dimensions
    const pixelCount = img.width * img.height
    
    // Hard limit - reject if way too large
    if (pixelCount > maxPixelsHardLimit) {
      error.value = `Image too large (${img.width}×${img.height} = ${Math.round(pixelCount / 1_000_000)}MP). Maximum ${Math.round(maxPixelsHardLimit / 1_000_000)}MP (approximately 7000×7000 pixels) supported.`
      isProcessing.value = false
      return
    }
    
    // Warn if approaching limit
    if (pixelCount > maxPixelsWarning) {
      console.warn(`Large image detected: ${img.width}×${img.height} (${Math.round(pixelCount / 1_000_000)}MP)`)
    }
    
    // Determine if we should process sequentially (for large images or low memory)
    const shouldProcessSequentially = pixelCount > 10_000_000 || (memoryInfo && memoryInfo.availableMB < 500)
    
    // Downscale if over target size (but still process it)
    const targetDimensions = downscaleImage(img, maxPixels)
    let canvas, ctx, imageData
    
    try {
      if (targetDimensions.scale < 1) {
        processingStatus.value = `Downscaling image from ${img.width}×${img.height} to ${targetDimensions.width}×${targetDimensions.height} (${Math.round(targetDimensions.scale * 100)}%)...`
        canvas = document.createElement('canvas')
        canvas.width = targetDimensions.width
        canvas.height = targetDimensions.height
        ctx = canvas.getContext('2d')
        ctx.drawImage(img, 0, 0, targetDimensions.width, targetDimensions.height)
        imageData = ctx.getImageData(0, 0, canvas.width, canvas.height)
        console.log(`Image downscaled from ${img.width}×${img.height} to ${targetDimensions.width}×${targetDimensions.height}`)
      } else {
        canvas = document.createElement('canvas')
        canvas.width = img.width
        canvas.height = img.height
        ctx = canvas.getContext('2d')
        ctx.drawImage(img, 0, 0)
        imageData = ctx.getImageData(0, 0, canvas.width, canvas.height)
      }
    } catch (canvasError) {
      if (canvasError.message && (
        canvasError.message.includes('memory') ||
        canvasError.message.includes('allocation') ||
        canvasError.name === 'RangeError'
      )) {
        throw new Error('Out of memory: Unable to create canvas. Image may be too large.')
      }
      throw canvasError
    }
    
    // Process formats (sequentially for large images to save memory, parallel for smaller ones)
    let webpResult, avifResult, pngResult, jpgResult
    
    if (shouldProcessSequentially) {
      // Process sequentially to reduce peak memory usage
      processingStatus.value = 'Processing WebP...'
      webpResult = await Promise.allSettled([processWebP(imageData)]).then(r => r[0])
      
      processingStatus.value = 'Processing AVIF...'
      avifResult = await Promise.allSettled([processAVIF(imageData)]).then(r => r[0])
      
      processingStatus.value = 'Processing PNG...'
      pngResult = await Promise.allSettled([processPNG(canvas)]).then(r => r[0])
      
      processingStatus.value = 'Processing JPG...'
      jpgResult = await Promise.allSettled([processJPG(imageData)]).then(r => r[0])
    } else {
      // Process in parallel for smaller images (faster)
      processingStatus.value = 'Processing all formats...'
      [webpResult, avifResult, pngResult, jpgResult] = await Promise.allSettled([
        processWebP(imageData),
        processAVIF(imageData),
        processPNG(canvas),
        processJPG(imageData)
      ])
    }
    
    processingStatus.value = 'Finalizing...'
    
    // Store original - use the data URL directly
    const originalUrl = imageDataUrl
    
    // Build results object
    results.value = {
      original: {
        url: originalUrl,
        size: originalSize.value,
        savings: 0
      },
      webp: webpResult.status === 'fulfilled' ? webpResult.value : { url: null, size: 0, savings: 0, error: webpResult.reason?.message },
      avif: avifResult.status === 'fulfilled' ? avifResult.value : { url: null, size: 0, savings: 0, error: avifResult.reason?.message },
      png: pngResult.status === 'fulfilled' ? pngResult.value : { url: null, size: 0, savings: 0, error: pngResult.reason?.message },
      jpg: jpgResult.status === 'fulfilled' ? jpgResult.value : { url: null, size: 0, savings: 0, error: jpgResult.reason?.message }
    }
    
    // Calculate savings for each format
    if (results.value.webp.size > 0) {
      results.value.webp.savings = Math.round(((originalSize.value - results.value.webp.size) / originalSize.value) * 100)
    }
    if (results.value.avif.size > 0) {
      results.value.avif.savings = Math.round(((originalSize.value - results.value.avif.size) / originalSize.value) * 100)
    }
    if (results.value.png.size > 0) {
      results.value.png.savings = Math.round(((originalSize.value - results.value.png.size) / originalSize.value) * 100)
    }
    if (results.value.jpg.size > 0) {
      results.value.jpg.savings = Math.round(((originalSize.value - results.value.jpg.size) / originalSize.value) * 100)
    }
    
    // Log any errors
    const errors = []
    if (webpResult.status === 'rejected') errors.push(`WebP: ${webpResult.reason?.message}`)
    if (avifResult.status === 'rejected') errors.push(`AVIF: ${avifResult.reason?.message}`)
    if (pngResult.status === 'rejected') errors.push(`PNG: ${pngResult.reason?.message}`)
    if (jpgResult.status === 'rejected') errors.push(`JPG: ${jpgResult.reason?.message}`)
    
    if (errors.length > 0) {
      console.warn('Some formats failed to process:', errors)
      if (errors.length === 4) {
        error.value = `All formats failed to process: ${errors.join(', ')}`
      }
    }
    
  } catch (err) {
    console.error('Processing error:', err)
    
    // Better error handling for out-of-memory errors
    if (err.message && (
      err.message.includes('memory') || 
      err.message.includes('allocation') ||
      err.message.includes('too large') ||
      err.name === 'RangeError'
    )) {
      error.value = `Out of memory error. The image may be too large. Try a smaller image or close other browser tabs.`
    } else if (err.message && err.message.includes('Canvas')) {
      error.value = `Canvas size limit exceeded. Image dimensions are too large.`
    } else {
      error.value = `Processing failed: ${err.message || 'Unknown error'}`
    }
  } finally {
    isProcessing.value = false
    processingStatus.value = ''
  }
}

const processWebP = async (imageData) => {
  try {
    const convertedData = await encodeWebP(imageData, {
      quality: quality.value
    })
    const blob = new Blob([convertedData], { type: 'image/webp' })
    const url = URL.createObjectURL(blob)
    return {
      url,
      size: blob.size
    }
  } catch (err) {
    console.error('WebP processing error:', err)
    throw err
  }
}

const processAVIF = async (imageData) => {
  try {
    const convertedData = await encodeAvif(imageData, {
      quality: quality.value
    })
    const blob = new Blob([convertedData], { type: 'image/avif' })
    const url = URL.createObjectURL(blob)
    return {
      url,
      size: blob.size
    }
  } catch (err) {
    console.error('AVIF processing error:', err)
    throw err
  }
}

const processPNG = async (canvas) => {
  try {
    // First, get PNG from canvas
    const pngBlob = await new Promise((resolve, reject) => {
      canvas.toBlob((blob) => {
        if (blob) {
          resolve(blob)
        } else {
          reject(new Error('Failed to create PNG blob'))
        }
      }, 'image/png')
    })
    
    // Convert blob to array buffer for oxipng
    const arrayBuffer = await pngBlob.arrayBuffer()
    
    // Optimize PNG with oxipng
    const optimizedBuffer = await optimisePng(arrayBuffer, {
      level: Math.min(6, Math.floor(quality.value / 20)) // Map quality 0-100 to oxipng level 0-6
    })
    
    const blob = new Blob([optimizedBuffer], { type: 'image/png' })
    const url = URL.createObjectURL(blob)
    return {
      url,
      size: blob.size
    }
  } catch (err) {
    console.error('PNG processing error:', err)
    throw err
  }
}

const processJPG = async (imageData) => {
  try {
    const convertedData = await encodeJpeg(imageData, {
      quality: quality.value
    })
    const blob = new Blob([convertedData], { type: 'image/jpeg' })
    const url = URL.createObjectURL(blob)
    return {
      url,
      size: blob.size
    }
  } catch (err) {
    console.error('JPG processing error:', err)
    throw err
  }
}

const reprocessAllFormats = async () => {
  if (!originalImage.value) return
  // Re-process with new quality setting
  await processAllFormats(originalImage.value, null)
}

const downloadFormat = (format) => {
  if (!results.value || !results.value[format] || !results.value[format].url) return
  
  const formatData = results.value[format]
  const link = document.createElement('a')
  link.href = formatData.url
  
  let extension = format
  if (format === 'original') {
    extension = originalFormat.value
  }
  
  link.download = `${fileName.value}.${extension}`
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

const getSavingsClass = (savings) => {
  if (savings > 0) return 'savings-positive'
  if (savings < 0) return 'savings-negative'
  return ''
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

.settings-section {
  margin-top: 1rem;
}

.input-group {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
}

.quality-hint {
  margin: 0;
  font-size: 0.85rem;
  color: var(--color-text-light);
  font-style: italic;
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

.loading-section {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 3rem;
  gap: 1rem;
}

.loading-spinner {
  width: 48px;
  height: 48px;
  border: 4px solid #e1e5e9;
  border-top-color: var(--color-accent);
  border-radius: 50%;
  animation: spin 1s linear infinite;
}

@keyframes spin {
  to { transform: rotate(360deg); }
}

.loading-text {
  color: var(--color-text);
  font-size: 1.1rem;
  margin: 0;
  font-weight: 500;
}

.loading-hint {
  color: var(--color-text-light);
  font-size: 0.9rem;
  margin: 0.5rem 0 0 0;
  font-style: italic;
}

.formats-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 1.5rem;
  margin-bottom: 2rem;
}

.format-card {
  background: #f8f9fa;
  border-radius: 12px;
  padding: 1.5rem;
  text-align: center;
  border: 2px solid #e1e5e9;
  display: flex;
  flex-direction: column;
  gap: 0.75rem;
  transition: all 0.3s ease;
}

.format-card:hover {
  border-color: var(--color-accent);
  transform: translateY(-2px);
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

.format-card--best {
  background: linear-gradient(135deg, var(--color-accent-light), var(--color-accent));
  border-color: var(--color-accent);
  color: var(--color-primary);
}

.format-card--best .format-card__label {
  color: var(--color-primary);
  font-weight: 600;
}

.format-card__header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 0.5rem;
}

.format-card__label {
  font-size: 1.1rem;
  font-weight: 600;
  color: var(--color-primary);
}

.format-card__format {
  font-size: 0.75rem;
  color: var(--color-text-light);
  text-transform: uppercase;
  font-weight: 600;
  padding: 0.25rem 0.5rem;
  background: rgba(0, 0, 0, 0.05);
  border-radius: 4px;
}

.format-card--best .format-card__format {
  background: rgba(0, 0, 0, 0.1);
  color: var(--color-primary);
}

.format-card__size {
  font-size: 1.5rem;
  font-weight: 700;
  color: var(--color-primary);
  margin: 0.5rem 0;
}

.format-card--best .format-card__size {
  color: var(--color-primary);
}

.format-card__savings {
  font-size: 0.9rem;
  font-weight: 500;
  margin-bottom: 0.5rem;
}

.savings-positive {
  color: #28a745;
}

.savings-negative {
  color: #dc3545;
}

.format-card__download {
  width: 100%;
  padding: 0.75rem 1rem;
  background: var(--color-primary);
  color: white;
  border: none;
  border-radius: 8px;
  font-size: 0.95rem;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 0.5rem;
  margin-top: auto;
}

.format-card__download:hover:not(:disabled) {
  background: var(--color-secondary);
  transform: translateY(-1px);
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
}

.format-card__download:disabled {
  background: #ccc;
  cursor: not-allowed;
  opacity: 0.6;
}

.format-card--best .format-card__download {
  background: var(--color-primary);
  color: white;
}

.format-card--best .format-card__download:hover:not(:disabled) {
  background: var(--color-secondary);
}

.download-icon {
  width: 18px;
  height: 18px;
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

@media (max-width: 1024px) {
  .formats-grid {
    grid-template-columns: repeat(2, 1fr);
  }
}

@media (max-width: 768px) {
  .formats-grid {
    grid-template-columns: 1fr;
    gap: 1rem;
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

