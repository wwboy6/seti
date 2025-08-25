
<script setup lang="ts">
import { RouterLink, RouterView } from 'vue-router'
</script>

<template>
  <header>
    <h1>SETI tools by Kay Chan</h1>
    <nav>
      <RouterLink to="/simulation">Simulation</RouterLink>
      <RouterLink to="/gallery">Gallery</RouterLink>
      <RouterLink to="/links">Links</RouterLink>
    </nav>
  </header>
  <main>
    <RouterView />
  </main>
</template>

<style scoped>
header {
  text-align: center;
  margin-top: 2rem;
}

h1 {
  font-size: 2rem;
  margin-bottom: 1.5rem;
}

nav {
  margin-bottom: 2rem;
}

nav a {
  font-size: 1.1rem;
  margin: 0 1rem;
  text-decoration: none;
  color: #42b983;
}

nav a.router-link-exact-active {
  font-weight: bold;
  color: #35495e;
}

main {
  min-height: 60vh;
}
</style>
