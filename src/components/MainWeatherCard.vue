<script setup>
import { defineProps } from 'vue'

defineProps({
  city: String,
  country: String,
  today: String,
  weatherIcon: String,
  temperature: Number,
})
</script>

<template>
  <div class="main-results card">
    <div class="location">{{ city }}, {{ country }}</div>
    <div class="date">{{ today }}</div>
    <div class="summary">
      <img :src="`/src/assets/images/icon-${weatherIcon}.webp`" alt="`${weatherIcon}-icon`" />
      <div class="temperature">{{ temperature }}°</div>
    </div>
  </div>
</template>
