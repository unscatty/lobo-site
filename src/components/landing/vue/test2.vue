<!-- <script> -->
<script setup>
// import { document } from 'global';
import { ref, onMounted } from 'vue'

const isTouchDevice = ref(false)
const hints = ref([])

onMounted(() => {
  // Detect if the device supports touch events
  isTouchDevice.value = 'ontouchstart' in document.documentElement
})

const handleMouseover = () => {
  if (!isTouchDevice.value) {
    hints.value.push('Mouseover event triggered')
    // Handle mouseover behavior
  }
}

const handleMouseClick = () => {
  if (!isTouchDevice.value) {
    hints.value.push('Mouse click event triggered')
    // Handle mouse click behavior
  }
}

const handleTouchStart = () => {
  if (isTouchDevice.value) {
    hints.value.push('Touchstart event triggered')
    // Handle touchstart behavior
  }
}
</script>

<template>
  <div
    @mouseover="handleMouseover"
    @click="handleMouseClick"
    @touchstart="handleTouchStart"
    class="touchable bg-white text-black"
  >
    Touch or Hover over me!
  </div>
  <p v-for="(hint, index) in hints" :key="index" text-white>{{ hint }}</p>
</template>

<style scoped>
.touchable {
  cursor: pointer; /* Set cursor to pointer for touchable elements */
}
</style>
