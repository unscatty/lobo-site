<script setup lang="ts">
import SpeakersSlider from './SpeakersSlider.vue'
</script>

<template>
  <Transition mode="out-in">
    <Suspense>
      <SpeakersSlider />
      <template #fallback>
        <div text-white>Loading...</div>
      </template>
    </Suspense>
  </Transition>
</template>

<style scoped></style>
