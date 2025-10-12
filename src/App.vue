<script setup>
import axios from 'axios'
import { ref } from 'vue'
import Button from './components/Button.vue'
import InputWithIcon from './components/InputWithIcon.vue'

const location = ref('')
const city = ref('')
const country = ref('')
const today = ref('')
const weatherIcon = ref('')
const temperature = ref('')

const latitude = ref('')
const longitude = ref('')

const feelsLike = ref('')
const humidity = ref('')
const wind = ref('')
const precipitation = ref('')

const weatherCodeMap = {
  0: 'sunny', // Clear sky
  1: 'partly-cloudy', // Mainly clear
  2: 'partly-cloudy', // Partly cloudy
  3: 'overcast', // Overcast
  45: 'fog', // Fog
  48: 'fog', // Depositing rime fog
  51: 'drizzle', // Light drizzle
  53: 'drizzle', // Moderate drizzle
  55: 'drizzle', // Heavy drizzle
  56: 'drizzle', // Light freezing drizzle
  57: 'drizzle', // Heavy freezing drizzle
  61: 'rain', // Light rain
  63: 'rain', // Moderate rain
  65: 'rain', // Heavy rain
  66: 'rain', // Light freezing rain
  67: 'rain', // Heavy freezing rain
  71: 'snow', // Light snow
  73: 'snow', // Moderate snow
  75: 'snow', // Heavy snow
  77: 'snow', // Snow grains
  80: 'rain', // Light rain showers
  81: 'rain', // Moderate rain showers
  82: 'rain', // Heavy rain showers
  85: 'snow', // Light snow showers
  86: 'snow', // Heavy snow showers
  95: 'storm', // Thunderstorm
  96: 'storm', // Thunderstorm with light hail
  99: 'storm', // Thunderstorm with heavy hail
}

const getWeather = async () => {
  try {
    const response = await axios.get(
      `https://geocoding-api.open-meteo.com/v1/search?name=${location.value}`,
    )
    const locationData = response.data.results[0]

    // Get 'Official' location name
    city.value = locationData.name
    country.value = locationData.country

    // Get coordinates
    latitude.value = locationData.latitude
    longitude.value = locationData.longitude
  } catch (error) {
    console.log(error)
  } finally {
    try {
      const response =
        await axios.get(`https://api.open-meteo.com/v1/forecast?latitude=${latitude.value}&longitude=${longitude.value}&daily=temperature_2m_max,temperature_2m_min,precipitation_sum,weathercode,apparent_temperature_max,relative_humidity_2m_max,wind_speed_10m_max,precipitation_sum
`)

      // Get the today date and format it
      today.value = new Intl.DateTimeFormat('en-US', {
        weekday: 'long',
        year: 'numeric',
        month: 'short',
        day: 'numeric',
      }).format(new Date(response.data.daily.time[0]))

      const weatherCode = response.data.daily.weathercode[0]
      weatherIcon.value = weatherCodeMap[weatherCode]

      temperature.value = Math.trunc(response.data.daily.temperature_2m_max[0])

      feelsLike.value = Math.trunc(response.data.daily.apparent_temperature_max[0])
      humidity.value = Math.trunc(response.data.daily.relative_humidity_2m_max[0])
      wind.value = Math.trunc(response.data.daily.wind_speed_10m_max[0])
      precipitation.value = Math.trunc(response.data.daily.precipitation_sum[0])
    } catch (error) {
      console.log(error)
    }
  }
}
</script>

<template>
  <!-- @TODO Header -->
  <header>
    <img src="@/assets/images/logo.svg" alt="Logo" />
  </header>

  <h1>How's the sky looking today?</h1>

  <form @submit.prevent="getWeather">
    <InputWithIcon
      :id="'search'"
      :type="'text'"
      :placeholder="'Search for a place...'"
      v-model="location"
    />

    <Button :type="'submit'" :buttonText="'Search'" />
  </form>

  <div class="main-results card">
    <div class="location">{{ city }}, {{ country }}</div>
    <div class="date">{{ today }}</div>
    <div class="summary">
      <img :src="`/src/assets/images/icon-${weatherIcon}.webp`" alt="`${weatherIcon}-icon`" />
      <div class="temperature">{{ temperature }}°</div>
    </div>
  </div>

  <div class="extra-details">
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
      <div class="value">{{ wind }} km/h</div>
    </div>
    <div class="card">
      <div class="title">Precipitation</div>
      <div class="value">{{ precipitation }} mm</div>
    </div>
  </div>
</template>
