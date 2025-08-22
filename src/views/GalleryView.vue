<template>
  <div class="gallery-container">
    <h2>Gallery</h2>
    <div class="gallery-section">
      <h3>Planet</h3>
      <div class="gallery-grid">
        <div v-for="img in planetImages" :key="img" class="gallery-item">
          <img :src="`/seti/img/${img}`" :alt="img" @click="openPopup(img)" />
        </div>
      </div>
      <transition name="backdrop-fade">
        <div v-if="popupImg" class="mui-backdrop" @click="closePopup">
          <img class="mui-backdrop-img" :src="`/seti/img/${popupImg}`" :alt="popupImg" @click.stop />
        </div>
      </transition>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
// List of all planet images (hardcoded for now, can be automated if needed)
const planetImages = [
  'planet-1-mercury-c.png',
  'planet-2-venus-c.png',
  'planet-3-mars-c.png',
  'planet-4-jupiter-c.png',
  'planet-5-saturn-c.png',
  'planet-6-uranus-c.png',
  'planet-7-neptune-c.png',
]

const popupImg = ref<string|null>(null)
function openPopup(img: string) {
  popupImg.value = img
}
function closePopup() {
  popupImg.value = null
}
</script>

<style scoped>
.gallery-container {
  max-width: 900px;
  margin: 0 auto;
}
.gallery-section {
  margin-bottom: 2.5rem;
}
.gallery-grid {
  display: flex;
  flex-wrap: wrap;
  gap: 1.5rem;
}
.gallery-item {
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 140px;
}
.gallery-item img {
  width: 120px;
  height: 120px;
  object-fit: contain;
  border-radius: 8px;
  background: #f8f8f8;
  border: 1px solid #eee;
}
.img-label {
  margin-top: 0.5rem;
  font-size: 0.95rem;
  color: #555;
  text-align: center;
  word-break: break-all;
}
.mui-backdrop {
  position: fixed;
  inset: 0;
  z-index: 1300;
  background-color: rgba(33, 33, 33, 0.7);
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: zoom-out;
  transition: background-color 0.3s;
}
.mui-backdrop-img {
  max-width: 90vw;
  max-height: 90vh;
  border-radius: 12px;
  box-shadow: 0 4px 32px #0008;
  background: #fff;
  cursor: auto;
  display: block;
  margin: auto;
  transition: box-shadow 0.3s, transform 0.3s;
}
.backdrop-fade-enter-active, .backdrop-fade-leave-active {
  transition: opacity 0.3s;
}
.backdrop-fade-enter-from, .backdrop-fade-leave-to {
  opacity: 0;
}
</style>