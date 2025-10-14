<script setup>
import { ref, defineEmits } from 'vue'
import axios from 'axios'

import InputWithIcon from './InputWithIcon.vue'
import Button from './Button.vue'

const emit = defineEmits()
const weatherData = ref({})

const location = ref('')

const latitude = ref('')
const longitude = ref('')

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
    weatherData.city = locationData.name
    weatherData.country = locationData.country

    // Get coordinates
    latitude.value = locationData.latitude
    longitude.value = locationData.longitude
  } catch (error) {
    console.log(error)
  } finally {
    try {
      const response =
        await axios.get(`https://api.open-meteo.com/v1/forecast?latitude=${latitude.value}&longitude=${longitude.value}&daily=temperature_2m_max,temperature_2m_min,precipitation_sum,weathercode,apparent_temperature_max,relative_humidity_2m_max,wind_speed_10m_max,precipitation_sum&hourly=temperature_2m,weathercode
`)

      // Get the today date and format it
      weatherData.today = new Intl.DateTimeFormat('en-US', {
        weekday: 'long',
        year: 'numeric',
        month: 'short',
        day: 'numeric',
      }).format(new Date(response.data.daily.time[0]))

      const weatherCode = response.data.daily.weathercode[0]
      weatherData.weatherIcon = weatherCodeMap[weatherCode]

      weatherData.temperature = Math.trunc(response.data.daily.temperature_2m_max[0])

      weatherData.feelsLike = Math.trunc(response.data.daily.apparent_temperature_max[0])
      weatherData.humidity = Math.trunc(response.data.daily.relative_humidity_2m_max[0])
      weatherData.wind = Math.trunc(response.data.daily.wind_speed_10m_max[0])
      weatherData.precipitation = Math.trunc(response.data.daily.precipitation_sum[0])

      // Get the data for the daily forecast
      weatherData.weeklyWeather = response.data.daily.time.map((dateStr, index) => {
        const day = new Date(dateStr).toLocaleDateString('en-US', { weekday: 'short' })

        return {
          day,
          weatherCode: response.data.daily.weathercode[index],
          weatherIcon: weatherCodeMap[response.data.daily.weathercode[index]],
          tempMin: Math.trunc(response.data.daily.temperature_2m_min[index]),
          tempMax: Math.trunc(response.data.daily.temperature_2m_max[index]),
        }
      })

      // Get the data for the hourly forecast
      // Group hourly data by day
      const hourlyDataGroupedByDay = []

      // Iterate through the hourly data and group it by the day
      response.data.hourly.time.forEach((hourStr, index) => {
        const hour = new Date(hourStr)
        const dayOfWeek = hour.toLocaleDateString('en-US', { weekday: 'long' })

        // Check if this day already exists in the grouped array
        let dayGroup = hourlyDataGroupedByDay.find((day) => day.day === dayOfWeek)
        if (!dayGroup) {
          dayGroup = { day: dayOfWeek, hours: [] }
          hourlyDataGroupedByDay.push(dayGroup)
        }

        // Push the hourly forecast into the correct day group
        const weatherCode = response.data.hourly.weathercode[index]
        const temperature = Math.trunc(response.data.hourly.temperature_2m[index])
        dayGroup.hours.push({
          hour: hour.toLocaleTimeString('en-US', { hour: 'numeric', hour12: true }),
          weatherCode: weatherCode,
          weatherIcon: weatherCodeMap[weatherCode],
          temperature: temperature,
        })
      })

      weatherData.hourlyWeather = hourlyDataGroupedByDay
    } catch (error) {
      console.log(error)
    }
  }

  emit('weatherResult', weatherData)
}
</script>

<template>
  <section>
    <form @submit.prevent="getWeather">
      <InputWithIcon
        :id="'search'"
        :type="'text'"
        :placeholder="'Search for a place...'"
        v-model="location"
      />

      <Button :type="'submit'" :buttonText="'Search'" />
    </form>
  </section>
</template>
