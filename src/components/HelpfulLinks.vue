<template>
  <div class="tool">
    <div class="tool__header">
      <h2 class="tool__title">Helpful Links</h2>
      <p class="tool__description">
        A curated collection of useful web development tools and resources.
      </p>
    </div>

    <div class="links-container">
      <div class="links-grid">
        <a
          v-for="link in links"
          :key="link.id"
          :href="link.url"
          target="_blank"
          rel="noopener noreferrer"
          class="link-card"
        >
          <div class="link-card__header">
            <h3 class="link-card__title">{{ link.title }}</h3>
            <svg class="link-card__icon" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M10 6H6a2 2 0 00-2 2v10a2 2 0 002 2h10a2 2 0 002-2v-4M14 4h6m0 0v6m0-6L10 14" />
            </svg>
          </div>
          <p class="link-card__description">{{ link.description }}</p>
          <div class="link-card__footer">
            <span class="link-card__url">{{ getDomain(link.url) }}</span>
            <span v-if="link.category" class="link-card__category">{{ link.category }}</span>
          </div>
        </a>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const links = ref([
  {
    id: 'cyberchef',
    title: 'CyberChef',
    url: 'https://gchq.github.io/CyberChef/',
    description: 'The Cyber Swiss Army Knife - a web app for encoding, decoding, encryption, compression, data analysis, and more. Perfect for cybersecurity tasks, data format conversion, and digital forensics.',
    category: 'Security & Data'
  }
])

const getDomain = (url) => {
  try {
    const urlObj = new URL(url)
    return urlObj.hostname.replace('www.', '')
  } catch {
    return url
  }
}
</script>

<style scoped>
.links-container {
  margin-top: 2rem;
}

.links-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
  gap: 1.5rem;
}

.link-card {
  display: flex;
  flex-direction: column;
  background: #f8f9fa;
  border: 2px solid #e1e5e9;
  border-radius: 12px;
  padding: 1.5rem;
  text-decoration: none;
  color: inherit;
  transition: all 0.3s ease;
  cursor: pointer;
}

.link-card:hover {
  border-color: var(--color-accent);
  background: #fff;
  transform: translateY(-2px);
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
}

.link-card__header {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  margin-bottom: 0.75rem;
  gap: 1rem;
}

.link-card__title {
  margin: 0;
  font-size: 1.25rem;
  font-weight: 600;
  color: var(--color-primary);
  flex: 1;
}

.link-card__icon {
  width: 20px;
  height: 20px;
  color: var(--color-text-light);
  flex-shrink: 0;
  transition: all 0.3s ease;
}

.link-card:hover .link-card__icon {
  color: var(--color-accent);
  transform: translate(2px, -2px);
}

.link-card__description {
  margin: 0 0 1rem 0;
  color: var(--color-text);
  font-size: 0.95rem;
  line-height: 1.6;
  flex: 1;
}

.link-card__footer {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-top: auto;
  padding-top: 1rem;
  border-top: 1px solid #e1e5e9;
}

.link-card__url {
  font-size: 0.85rem;
  color: var(--color-text-light);
  font-family: 'IBM Plex Mono', 'Monaco', 'Menlo', 'Ubuntu Mono', monospace;
}

.link-card__category {
  font-size: 0.75rem;
  color: var(--color-accent);
  background: var(--color-accent-light);
  padding: 0.25rem 0.5rem;
  border-radius: 4px;
  font-weight: 500;
  text-transform: uppercase;
}

@media (max-width: 768px) {
  .links-grid {
    grid-template-columns: 1fr;
    gap: 1rem;
  }
  
  .link-card {
    padding: 1.25rem;
  }
}
</style>

