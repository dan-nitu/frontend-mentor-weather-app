<script setup>
import { defineProps, ref } from 'vue'

defineProps({
  hourlyWeather: Array,
})

const selectedDate = ref(0)
</script>

<template>
  <section class="card hourly-forecast">
    <div class="scroll-wrapper">
      <div class="controls-wrapper">
        <div>Hourly forecast</div>

        <select v-model="selectedDate">
          <option v-for="(day, index) in hourlyWeather" :key="index" :value="index">
            {{ day.day }}
          </option>
        </select>
      </div>

      <div class="cards-wrapper">
        <div class="card" v-for="data in hourlyWeather[selectedDate].hours">
          <div>
            <div class="image-wrapper">
              <img :src="`/assets/images/icon-${data.weatherIcon}.webp`" :alt="data.weatherIcon" />
            </div>
            <span>{{ data.hour }}</span>
          </div>
          <div>{{ data.temperature }}°</div>
        </div>
      </div>
    </div>
  </section>
</template>
