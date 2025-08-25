<template>
  <div class="links-container">
    <h2>Links</h2>
    <ul class="links-list">
      <li v-for="link in links" :key="link.url">
        <a :href="link.url" target="_blank" rel="noopener">{{ link.label }}</a>
      </li>
    </ul>
  </div>
</template>

<script setup lang="ts">
const links = [
  { label: 'Randomizer', url: 'https://seti-solarsystem.czechgames.com/' },
  { label: 'Card DB', url: 'https://seti.ender-wiggin.com/' },
  { label: 'Official FAQ', url: 'https://boardgamegeek.com/thread/3392878/official-faq-plus-cards-clarification' },
]
</script>

<style scoped>
.links-container {
  max-width: 600px;
  margin: 0 auto;
  padding: 2rem 1rem;
}
.links-list {
  list-style: none;
  padding: 0;
}
.links-list li {
  margin-bottom: 1.2rem;
}
.links-list a {
  font-size: 1.15rem;
  color: #1976d2;
  text-decoration: underline;
  transition: color 0.2s;
}
.links-list a:hover {
  color: #0d47a1;
}
</style>
