<template>
  <div class="simulation-container">
    <h2>Solar Simulation</h2>
    <div class="rotation-inputs">
      <label v-for="i in 3" :key="i" class="rotation-label">
        <input
          type="number"
          :value="rotations[i]"
          @input="onRotationInput($event, i)"
        />
      </label>
    </div>
    <div class="solar-stack">
      <img
        v-for="(img, idx) in images"
        :key="img"
        :src="`/seti/img/${img}`"
        :alt="img"
        class="solar-image wheel"
        :style="rotationStyle(idx)"
      />
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'

// Images from bottom to top
const images = [
  'wheel4.png',
  'wheel3.png',
  'wheel2.png',
  'wheel1.png',
  'sun.png',
]

// Array of 5 integers, 0-7, for rotation state of each image
const rotations = ref([0, 0, 0, 0, 0])
const initialRotations = [0, 3, 2, 5, 0]

function onRotationInput(event: Event, idx: number) {
  const val = parseInt((event.target as HTMLInputElement).value, 10)
  // Loop value to 0-7
  const mod = ((val % 8) + 8) % 8
  rotations.value[idx] = mod
}

// Helper to compute rotation style for each image
function rotationStyle(idx: number) {
  // 8 steps, so 360/8 = 45deg per step
  const deg = (rotations.value[idx] + initialRotations[idx]) % 8 * -45
  return {
    transform: `scale(0.3) rotate(${deg}deg)`
  }
}
</script>

<style scoped>
.simulation-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  min-height: 60vh;
}

h2 {
  margin-bottom: 2rem;
}

.rotation-inputs {
  display: flex;
  gap: 1rem;
  margin-bottom: 1.5rem;
}

.rotation-label input {
  width: 3rem;
  text-align: center;
  font-size: 1.1rem;
  padding: 0.2rem 0.4rem;
}

.solar-stack {
  position: relative;
  width: 360px;
  height: 360px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.solar-image.wheel {
  position: absolute;
  object-fit: contain;
  pointer-events: none;
}
</style>
