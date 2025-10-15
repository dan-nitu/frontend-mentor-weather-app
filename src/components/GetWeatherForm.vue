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

const getCoordinates = async () => {
  const response = await axios.get(
    `https://geocoding-api.open-meteo.com/v1/search?name=${location.value}`,
  )
  const data = response.data.results[0]

  latitude.value = data.latitude
  longitude.value = data.longitude

  return {
    city: data.name,
    country: data.country,
  }
}

const transformHourlyData = (hourly) => {
  const groupedData = []

  hourly.time.forEach((hourStr, index) => {
    const date = new Date(hourStr)
    const day = date.toLocaleDateString('en-US', { weekday: 'long' })

    let dayGroup = groupedData.find((d) => d.day === day)
    if (!dayGroup) {
      dayGroup = { day, hours: [] }
      groupedData.push(dayGroup)
    }

    dayGroup.hours.push({
      hour: date.toLocaleTimeString('en-US', { hour: 'numeric', hour12: true }),
      weatherCode: hourly.weathercode[index],
      weatherIcon: weatherCodeMap[hourly.weathercode[index]],
      temperature: Math.trunc(hourly.temperature_2m[index]),
    })
  })

  return groupedData
}

const getWeatherForecast = async () => {
  const url = `https://api.open-meteo.com/v1/forecast?latitude=${latitude.value}&longitude=${longitude.value}&daily=temperature_2m_max,temperature_2m_min,precipitation_sum,weathercode,apparent_temperature_max,relative_humidity_2m_max,wind_speed_10m_max&hourly=temperature_2m,weathercode`

  const { data } = await axios.get(url)

  const weatherCode = data.daily.weathercode[0]
  const todayDate = new Date(data.daily.time[0])
  const formattedDate = todayDate.toLocaleDateString('en-US', {
    weekday: 'long',
    year: 'numeric',
    month: 'short',
    day: 'numeric',
  })

  const weeklyWeather = data.daily.time.map((dateStr, i) => ({
    day: new Date(dateStr).toLocaleDateString('en-US', { weekday: 'short' }),
    weatherCode: data.daily.weathercode[i],
    weatherIcon: weatherCodeMap[data.daily.weathercode[i]],
    tempMin: Math.trunc(data.daily.temperature_2m_min[i]),
    tempMax: Math.trunc(data.daily.temperature_2m_max[i]),
  }))

  const hourlyWeather = transformHourlyData(data.hourly)

  return {
    today: formattedDate,
    weatherIcon: weatherCodeMap[weatherCode],
    temperature: Math.trunc(data.daily.temperature_2m_max[0]),
    feelsLike: Math.trunc(data.daily.apparent_temperature_max[0]),
    humidity: Math.trunc(data.daily.relative_humidity_2m_max[0]),
    wind: Math.trunc(data.daily.wind_speed_10m_max[0]),
    precipitation: Math.trunc(data.daily.precipitation_sum[0]),
    weeklyWeather,
    hourlyWeather,
  }
}

const getWeather = async () => {
  try {
    const { city, country } = await getCoordinates()
    const forecast = await getWeatherForecast()

    const weatherData = {
      city,
      country,
      ...forecast,
    }

    emit('weatherResult', weatherData)
  } catch (error) {
    console.error('Error fetching weather:', error)
  }
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
