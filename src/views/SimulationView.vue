<template>
  <div class="simulation-container">
    <h2>Solar Simulation</h2>
    <div class="rotation-inputs" @paste="onPaste">
      <label v-for="i in 3" :key="i" class="rotation-label">
        <input
          type="number"
          :value="rotations[i]"
          @input="onRotationInput($event, i)"
          :data-rotation-idx="i"
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
    <button class="toggle-btn" @click="showButtons = !showButtons">
      {{ showButtons ? 'Hide' : 'Show' }} Quick Controls
    </button>
    <div class="button-rows" v-show="showButtons">
      <!-- First row: small -->
      <div class="button-row">
        <button @click="incInputs([3], 1)">+small</button>
        <button @click="incInputs([3], -1)">-small</button>
      </div>
      <!-- Second row: middle -->
      <div class="button-row">
        <button @click="incInputs([2,3], 1)">+middle</button>
        <button @click="incInputs([2,3], -1)">-middle</button>
      </div>
      <!-- Third row: large -->
      <div class="button-row">
        <button @click="incInputs([1,2,3], 1)">+large</button>
        <button @click="incInputs([1,2,3], -1)">-large</button>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, nextTick } from 'vue'
const showButtons = ref(false)

function normalizeRotation(val: number): number {
  return ((val % 8) + 8) % 8
}

function incInputs(indices: number[], delta: number) {
  indices.forEach(idx => {
    rotations.value[idx] = normalizeRotation(rotations.value[idx] + delta)
  })
}

// Handle paste event for spreadsheet-like input
function onPaste(event: ClipboardEvent) {
  const active = document.activeElement as HTMLInputElement | null
  if (!active) return
  const idx = Number(active.getAttribute('data-rotation-idx'))
  if (isNaN(idx)) return
  const text = event.clipboardData?.getData('text') || ''
  // Split by tab or whitespace
  const values = text.split(/\s+/).filter(Boolean)
  if (values.length < 2) return // Only handle multi-cell paste
  console.warn('Pasting values:', values, 'at index:', idx)
  event.preventDefault()
  values.forEach((v, i) => {
    const val = parseInt(v, 10)
    if (!isNaN(val) && idx + i <= 3) {
      rotations.value[idx + i] = normalizeRotation(val)
    }
  })
  // Optionally, move focus to the last input
  nextTick(() => {
    const inputs = document.querySelectorAll<HTMLInputElement>('.rotation-label input')
    if (inputs[idx + values.length - 1]) {
      inputs[idx + values.length - 1].focus()
    }
  })
}

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
  rotations.value[idx] = normalizeRotation(val)
}

// Helper to compute rotation style for each image
function rotationStyle(idx: number) {
  // 8 steps, so 360/8 = 45deg per step
  const deg = normalizeRotation(rotations.value[idx] + initialRotations[idx]) * -45
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

.button-rows {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
  margin-bottom: 1.5rem;
}

.toggle-btn {
  margin-bottom: 0.5rem;
  padding: 0.3rem 1.2rem;
  font-size: 1rem;
  border-radius: 4px;
  border: 1px solid #aaa;
  background: #f8f8f8;
  cursor: pointer;
  transition: background 0.2s;
}
.toggle-btn:hover {
  background: #e0e0e0;
}

.button-row {
  display: flex;
  gap: 1rem;
  justify-content: center;
}

button {
  padding: 0.3rem 1.2rem;
  font-size: 1rem;
  border-radius: 4px;
  border: 1px solid #aaa;
  background: #f8f8f8;
  cursor: pointer;
  transition: background 0.2s;
}
button:hover {
  background: #e0e0e0;
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
