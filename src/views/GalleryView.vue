<template>
  <div class="gallery-container">
    <h2>Gallery</h2>
    <div class="gallery-toggle-row">
      <button
        v-for="g in galleries"
        :key="g.key"
        :class="['gallery-toggle-btn', { active: selectedGallery === g.key }]"
        @click="selectedGallery = g.key"
      >
        {{ g.label }}
      </button>
    </div>
    <div v-if="selectedGallery === 'planet'" class="gallery-section">
      <h3>Planet</h3>
      <div class="gallery-grid">
        <div v-for="img in planetImages" :key="img" class="gallery-item">
          <img :src="`/seti/img/${img}`" :alt="img" @click="openPopup(img)" />
        </div>
      </div>
    </div>
    <div v-if="selectedGallery === 'alien'" class="gallery-section">
      <h3>Alien</h3>
      <div class="gallery-grid">
        <div v-for="img in alienImages" :key="img" class="gallery-item">
          <img :src="`/seti/img/${img}`" :alt="img" @click="openPopup(img)" />
        </div>
      </div>
    </div>
    <div v-if="selectedGallery === 'solar'" class="gallery-section">
      <h3>Solar</h3>
      <div class="gallery-grid">
        <div v-for="img in solarImages" :key="img" class="gallery-item">
          <img :src="`/seti/img/${img}`" :alt="img" @click="openPopup(img)" />
        </div>
      </div>
    </div>
    <div v-if="selectedGallery === 'milestone'" class="gallery-section">
      <h3>Milestone</h3>
      <div class="gallery-grid">
        <div v-for="img in milestoneImages" :key="img" class="gallery-item">
          <img :src="`/seti/img/${img}`" :alt="img" @click="openPopup(img)" />
        </div>
      </div>
    </div>
    <transition name="backdrop-fade">
      <div v-if="popupImg" class="mui-backdrop" @click="closePopup">
        <img class="mui-backdrop-img" :src="`/seti/img/${popupImg}`" :alt="popupImg" @click.stop />
      </div>
    </transition>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
const planetImages = [
  'planet-1-mercury-c.png',
  'planet-2-venus-c.png',
  'planet-3-mars-c.png',
  'planet-4-jupiter-c.png',
  'planet-5-saturn-c.png',
  'planet-6-uranus-c.png',
  'planet-7-neptune-c.png',
]
const solarImages = [
  'wheel1.png',
  'wheel2.png',
  'wheel3.png',
  'wheel4.png',
]
const milestoneImages = [
  'ms1.PNG',
  'ms2.PNG',
  'ms3.PNG',
  'ms4.PNG',
  'ms5.PNG',
  'ms6.PNG',
  'ms7.PNG',
  'ms8.PNG',
]
// List of all planet and alien images (hardcoded for now)
const alienImages = [
  'et-anomalies.PNG',
  'et-centaurians.PNG',
  'et-exertians.PNG',
  'et-mascamites.PNG',
  'et-oumuamua.PNG',
]
const galleries = [
  { key: 'planet', label: 'Planet' },
  { key: 'alien', label: 'Alien' },
  { key: 'solar', label: 'Solar' },
  { key: 'milestone', label: 'Milestone' },
]
const selectedGallery = ref('planet')

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
.gallery-toggle-row {
  display: flex;
  gap: 1.5rem;
  justify-content: left;
  margin-bottom: 2rem;
}
.gallery-toggle-btn {
  padding: 0.5rem 1.5rem;
  font-size: 1.1rem;
  border-radius: 20px;
  border: 1px solid #bbb;
  background: #f8f8f8;
  color: #333;
  cursor: pointer;
  transition: background 0.2s, color 0.2s, border 0.2s;
}
.gallery-toggle-btn.active {
  background: #1976d2;
  color: #fff;
  border-color: #1976d2;
}
</style>