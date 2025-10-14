<script setup>
import { ref } from 'vue'

import TheHeader from './components/TheHeader.vue'
import TheTitle from './components/TheTitle.vue'
import GetWeatherForm from './components/GetWeatherForm.vue'
import MainWeatherCard from './components/MainWeatherCard.vue'
import ExtraDetails from './components/ExtraDetails.vue'
import DailyForecast from './components/DailyForecast.vue'
import HourlyForecast from './components/HourlyForecast.vue'

const weatherData = ref('')

const handleWeatherData = (data) => {
  weatherData.value = data
  console.log('hourlyWeather')
  console.log(weatherData)
}
</script>

<template>
  <TheHeader />

  <TheTitle />

  <GetWeatherForm @weatherResult="handleWeatherData" />

  <template v-if="weatherData">
    <MainWeatherCard
      :city="weatherData.city"
      :country="weatherData.country"
      :today="weatherData.today"
      :weatherIcon="weatherData.weatherIcon"
      :temperature="weatherData.temperature"
    />

    <ExtraDetails
      :feelsLike="weatherData.feelsLike"
      :humidity="weatherData.humidity"
      :wind="weatherData.wind"
      :precipitation="weatherData.precipitation"
    />

    <DailyForecast :weeklyWeather="weatherData.weeklyWeather" />

    <HourlyForecast :hourlyWeather="weatherData.hourlyWeather" />
  </template>
</template>
