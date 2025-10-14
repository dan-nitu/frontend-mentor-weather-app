<script setup>
import { defineProps } from 'vue'

defineProps({
  hourlyWeather: Array,
})
</script>

<template>
  <section class="card hourly-forecast">
    <div class="scroll-wrapper">
      <div>Hourly forecast</div>

      <div class="cards-wrapper">
        <div class="card" v-for="data in hourlyWeather[0].hours">
          <div>
            <div class="image-wrapper">
              <img
                :src="`/src/assets/images/icon-${data.weatherIcon}.webp`"
                :alt="data.weatherIcon"
              />
            </div>
            <span>{{ data.hour }}</span>
          </div>
          <div>{{ data.temperature }}°</div>
        </div>
      </div>
    </div>
  </section>
</template>
