<template>
  <div class="simulation-container">
    <h2>Solar Simulation</h2>
    <div class="sector-rotation-inputs">
      <span>Sector</span>
      <label v-for="(_, i) in sectorRotations" :key="i" class="rotation-label">
        <input
          type="number"
          :value="sectorRotations[i]"
          @input="onSectorRotationInput($event, i)"
          :data-sector-rotation-idx="i"
        />
      </label>
    </div>
    <div class="rotation-inputs" @paste="onPaste">
      <span>Planet</span>
      <label v-for="i in 3" :key="i" class="rotation-label">
        <input
          type="number"
          :value="rotations[i]"
          @input="onRotationInput($event, i)"
          :data-rotation-idx="i"
        />
      </label>
      <button class="random-btn" @click="randomizeRotations">random</button>
    </div>
    <div class="spin-row">
      <span class="spin-label" :class="spinTypeClass" >{{ spinTypeLabel }}</span>
      <input
        type="number"
        v-model.number="spinNumber"
        min="1"
        class="spin-input"
        placeholder="spin number"
        style="width: 3rem; margin-right: 0.5rem;"
      />
      <button class="spin-button" @click="onSpinInc">+spin</button>
      <button class="spin-button" @click="onSpinDec">-spin</button>
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
      <div
        v-for="(img, idx) in sectorImages"
        :key="img"
        class="solar-image"
        :style="sectorStyle(idx)"
      >
      
        <img class="sector-name" :src="`/seti/img/${img}-name.png`"/>
        <img :src="`/seti/img/${img}.png`"/>
      </div>
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
import { ref, nextTick, computed, watch, onMounted } from 'vue'

const spinType = computed(() => {
  const rem = spinNumber.value % 3
  if (rem === 1) return 'small'
  if (rem === 2) return 'middle'
  return 'large'
})

const spinTypeLabel = computed(() => {
  if (spinType.value === 'small') return 'small'
  if (spinType.value === 'middle') return 'middle'
  return 'large'
})

const spinTypeClass = computed(() => {
  if (spinType.value === 'small') return 'spin-label-small'
  if (spinType.value === 'middle') return 'spin-label-middle'
  return 'spin-label-large'
})


const showButtons = ref(false)
const spinNumber = ref(1)

const LOCAL_STORAGE_KEY = 'seti-simulation-state-v1'

function randomizeRotations() {
  for (let i = 1; i <= 3; i++) {
    rotations.value[i] = Math.floor(Math.random() * 8)
  }
  const minSectorRotation = Math.floor(Math.random() * 2)
  for (let i = 0; i < 4; i++) {
    sectorRotations.value[i] = minSectorRotation + i * 2
  }
  sectorRotations.value = sectorRotations.value
    .map(value => ({ value, sort: Math.random() }))
    .sort((a, b) => a.sort - b.sort)
    .map(({ value }) => value)
  spinNumber.value = 1
}

function onSpinInc() {
  const rem = spinNumber.value % 3
  if (rem === 1) {
    incInputs([3], 1)
  } else if (rem === 2) {
    incInputs([2,3], 1)
  } else {
    incInputs([1,2,3], 1)
  }
  spinNumber.value++
}

function onSpinDec() {
  if (spinNumber.value <= 1) return
  spinNumber.value--
  const rem = spinNumber.value % 3
  if (rem === 1) {
    incInputs([3], -1)
  } else if (rem === 2) {
    incInputs([2,3], -1)
  } else {
    incInputs([1,2,3], -1)
  }
}

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
    transform: `rotate(${deg}deg)`
  }
}


const sectorImages = ['sector1', 'sector2', 'sector3', 'sector4']
const sectorRotations = ref([0, 2, 4, 6])

// --- Persistence logic ---
function saveState() {
  const state = {
    rotations: rotations.value,
    sectorRotations: sectorRotations.value,
    spinNumber: spinNumber.value,
  }
  localStorage.setItem(LOCAL_STORAGE_KEY, JSON.stringify(state))
}

function loadState() {
  const raw = localStorage.getItem(LOCAL_STORAGE_KEY)
  if (!raw) return
  try {
    const state = JSON.parse(raw)
    if (Array.isArray(state.rotations) && state.rotations.length === 5) {
      rotations.value = state.rotations.slice()
    }
    if (Array.isArray(state.sectorRotations) && state.sectorRotations.length === 4) {
      sectorRotations.value = state.sectorRotations.slice()
    }
    if (typeof state.spinNumber === 'number') {
      spinNumber.value = state.spinNumber
    }
  } catch (e) {
    console.error('Failed to load simulation state:', e);
    console.warn('Save data:', raw);
  }
}

onMounted(() => {
  loadState()
})

watch([rotations, sectorRotations, spinNumber], saveState, { deep: true })

function sectorStyle(idx: number) {
  // 8 steps, so 360/8 = 45deg per step
  const deg = normalizeRotation(sectorRotations.value[idx]) * -45
  return {
    transform: `rotate(${deg}deg) translate(0,-385px)`
  }
}

function onSectorRotationInput(event: Event, idx: number) {
  const val = parseInt((event.target as HTMLInputElement).value, 10)
  sectorRotations.value[idx] = normalizeRotation(val)
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

.spin-row {
  display: flex;
  align-items: center;
  justify-content: center;
  margin-bottom: 1.5rem;
  gap: 0.5rem;
}
.spin-label {
  display: inline-block;
  width: 76px;
  text-align: center;
  font-weight: bold;
  font-size: 1.05rem;
  border-radius: 6px;
  margin-right: 0.5rem;
  padding: 0.2rem 0.5rem;
  letter-spacing: 0.5px;
  border: 1px solid #eee;
}
.spin-label-small {
  background: #fff9d6;
  color: #b8860b;
}
.spin-label-middle {
  background: #ffe5cc;
  color: #d2691e;
}
.spin-label-large {
  background: #e6f0ff;
  color: #1e3a8a;
}
.spin-input {
  font-size: 1.1rem;
  padding: 0.2rem 0.4rem;
  text-align: center;
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
  padding: 0.3rem .6rem;
  width: 100%;
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

.spin-button {
  width: 5em;
}

h2 {
  margin-bottom: 2rem;
}

.rotation-inputs, .sector-rotation-inputs {
  display: flex;
  gap: 1rem;
  margin-bottom: 1.5rem;
}
.sector-rotation-inputs {
  margin-bottom: 0.4rem;
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
  margin-bottom: 2rem;
}

.solar-image {
  position: absolute;
  object-fit: contain;
  pointer-events: none;
  scale: .3;
}
.sector-name {
  position: absolute;
  width: 100%;
}
.rotation-inputs {
  align-items: center;
}
.random-btn {
  margin-left: 1rem;
  padding: 0.2rem 1rem;
  font-size: 1rem;
  border-radius: 4px;
  border: 1px solid #aaa;
  background: #f0f8ff;
  color: #1e3a8a;
  cursor: pointer;
  transition: background 0.2s;
}
.random-btn:hover {
  background: #dbeafe;
}
</style>
