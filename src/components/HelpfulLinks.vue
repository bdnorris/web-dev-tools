<template>
  <div class="tool">
    <div class="tool__header">
      <h2 class="tool__title">Helpful Links</h2>
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
            <svg class="link-card__icon" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor" aria-hidden="true">
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
    description: 'Encode, decode, encrypt, compress, and inspect data in the browser.',
    category: 'Security & Data'
  },
  {
    id: 'word-counter',
    url: 'https://word-counter.co/',
    title: 'Word Counter',
    description: 'Count words, characters, sentences, and paragraphs.',
    category: 'Writing & Productivity'
  },
  {
    id: 'prettythumb',
    url: 'https://prettythumb.com/',
    title: 'PrettyThumb',
    description: 'Generate project thumbnails.',
    category: 'Design & Media'
  },
  {
    id: 'favicon-checker',
    url: 'https://colinkeany.github.io/favicon-checker/',
    title: 'Favicon Checker',
    description: 'See how a site’s favicon looks across platforms.',
    category: 'Design & Media'
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
  background: var(--color-inset-paper);
  border: 2px solid var(--color-hairline);
  border-radius: 12px;
  padding: 1.5rem;
  text-decoration: none;
  color: inherit;
  transition: border-color var(--ease-standard), background-color var(--ease-standard), box-shadow var(--ease-standard), transform var(--ease-standard);
  cursor: pointer;
}

.link-card:hover {
  border-color: var(--color-accent);
  background: var(--color-white);
  transform: translateY(-2px);
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
}

.link-card:focus-visible {
  outline-offset: 2px;
  border-color: var(--color-safety-orange);
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
  line-height: 1.3;
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
  font-size: 1rem;
  line-height: 1.6;
  flex: 1;
}

.link-card__footer {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-top: auto;
  padding-top: 1rem;
  border-top: 1px solid var(--color-hairline);
}

.link-card__url {
  font-size: 0.9rem;
  color: var(--color-text-light);
  font-family: var(--font-mono, 'IBM Plex Mono', 'Monaco', 'Menlo', 'Ubuntu Mono', monospace);
}

.link-card__category {
  font-size: 0.75rem;
  color: var(--color-accent);
  background: var(--color-accent-light);
  padding: 0.25rem 0.5rem;
  border-radius: 4px;
  font-weight: 500;
  text-transform: uppercase;
  letter-spacing: 0.04em;
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

