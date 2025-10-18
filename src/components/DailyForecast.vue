<script setup>
import { defineProps } from 'vue'

defineProps({
  weeklyWeather: Array,
})
</script>

<template>
  <section class="daily-forecast">
    <div>Daily forecast</div>

    <div class="cards-wrapper">
      <div class="card" v-for="daily in weeklyWeather" :key="daily.day">
        <div class="day">{{ daily.day }}</div>
        <img :src="`/assets/images/icon-${daily.weatherIcon}.webp`" :alt="daily.weatherIcon" />
        <div class="temperatures">
          <span>{{ daily.tempMax }}°</span>
          <span>{{ daily.tempMin }}°</span>
        </div>
      </div>
    </div>
  </section>
</template>
