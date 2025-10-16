<script setup>
import { ref, defineEmits } from 'vue'
import axios from 'axios'

import { weatherCodeMap } from '@/utils/weatherCodeMap.js'

import InputWithIcon from './InputWithIcon.vue'
import Button from './Button.vue'

const suggestions = ref([])
const selectedLocation = ref(null)

const location = ref('')
const latitude = ref('')
const longitude = ref('')

const emit = defineEmits()

const getCoordinates = async () => {
  const response = await axios.get(
    `https://geocoding-api.open-meteo.com/v1/search?name=${location.value}`,
  )

  suggestions.value = response.data.results || []
}

const selectLocation = (item) => {
  selectedLocation.value = item
  location.value = `${item.name}, ${item.country}` // update input
  suggestions.value = [] // hide suggestions
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
  const locationData = selectedLocation.value
  if (!locationData) return

  latitude.value = locationData.latitude
  longitude.value = locationData.longitude

  const url = `https://api.open-meteo.com/v1/forecast?latitude=${latitude.value}&longitude=${longitude.value}&daily=temperature_2m_max,temperature_2m_min,precipitation_sum,weathercode,apparent_temperature_max,relative_humidity_2m_max,wind_speed_10m_max&hourly=temperature_2m,weathercode`

  const { data } = await axios.get(url)

  console.log(data)

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
    if (!selectedLocation.value) {
      console.warn('No location selected.')
      return
    }

    const forecast = await getWeatherForecast()

    const { name: city, country } = selectedLocation.value

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
      <div class="input-wrapper input-with-suggestions">
        <InputWithIcon
          :id="'search'"
          :type="'text'"
          :placeholder="'Search for a place...'"
          v-model="location"
          @input="getCoordinates"
        />

        <ul v-if="suggestions.length" class="suggestions">
          <li v-for="(item, index) in suggestions" :key="index" @click="selectLocation(item)">
            {{ item.name }}, {{ item.admin1 ?? '' }}, {{ item.country }}
          </li>
        </ul>
      </div>

      <Button :type="'submit'" :buttonText="'Search'" :disabled="!selectedLocation" />
    </form>
  </section>
</template>
