<script setup>
import axios from 'axios'
import { ref } from 'vue'
import Button from './components/Button.vue'

const location = ref('')
const city = ref('')
const country = ref('')
const temperature = ref('')

const getWeather = async () => {
  try {
    const response = await axios.get(
      `https://geocoding-api.open-meteo.com/v1/search?name=${location.value}`,
    )
    console.log(response.data.results[0])
    const data = response.data.results[0]
    city.value = data.name
    country.value = data.country
  } catch (error) {
    console.log(error)
  }
}
</script>

<template>
  <header>
    <img src="@/assets/images/logo.svg" alt="Logo" />
  </header>

  <h1>How's the sky looking today?</h1>

  <form @submit.prevent="getWeather">
    <div class="input-with-icon">
      <label for="search" class="icon search"></label>
      <input id="search" type="text" v-model="location" placeholder="Search for a place..." />
    </div>
    <Button :type="'submit'" :buttonText="'Search'" />
  </form>

  <div>City: {{ city }}</div>
  <div>Country: {{ country }}</div>
  <div>Temperature: {{ temperature }}</div>
</template>
