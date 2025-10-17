<script setup>
import { defineProps } from 'vue'

defineProps({
  feelsLike: { type: Number, default: '' },
  humidity: { type: Number, default: '' },
  wind: { type: Number, default: '' },
  precipitation: { type: Number, default: '' },
  units: String,
})
</script>

<template>
  <section class="extra-details">
    <div class="card">
      <div class="title">Feels Like</div>
      <div class="value">{{ feelsLike }}°</div>
    </div>
    <div class="card">
      <div class="title">Humidity</div>
      <div class="value">{{ humidity }}%</div>
    </div>
    <div class="card">
      <div class="title">Wind</div>
      <div class="value">{{ wind }} {{ units === 'metric' ? 'km/h' : 'mph' }}</div>
    </div>
    <div class="card">
      <div class="title">Precipitation</div>
      <div class="value">{{ precipitation }} {{ units === 'metric' ? 'mm' : 'inch' }}</div>
    </div>
  </section>
</template>
