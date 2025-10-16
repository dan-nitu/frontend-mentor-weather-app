<script setup>
import { ref, defineEmits } from 'vue'

const showOptions = ref(false)

const imperialUnits = ref(false)
const temperature = ref('celsius')
const windSpeed = ref('kmh')
const precipitation = ref('mm')

const toggleOptions = () => {
  showOptions.value = !showOptions.value
}

const toggleUnits = () => {
  if (!imperialUnits.value) {
    temperature.value = 'fahrenheit'
    windSpeed.value = 'mph'
    precipitation.value = 'inch'
    imperialUnits.value = true
  } else {
    temperature.value = 'celsius'
    windSpeed.value = 'kmh'
    precipitation.value = 'mm'
    imperialUnits.value = false
  }
}
</script>

<template>
  <!-- @TODO Header -->
  <header>
    <a href="/">
      <img src="@/assets/images/logo.svg" alt="Logo" />
    </a>

    <div class="toggle-button-wrapper">
      <button class="has-icon cog arrow-down" @click="toggleOptions">Units</button>

      <div class="options" v-if="showOptions">
        <button @click="toggleUnits">Switch to {{ imperialUnits ? 'Metric' : 'Imperial' }}</button>

        <fieldset>
          <span>Temperature</span>

          <div>
            <input
              type="radio"
              id="celsius"
              name="temperature"
              value="celsius"
              v-model="temperature"
            />
            <label for="celsius">Celsius (°C)</label>
          </div>

          <div>
            <input
              type="radio"
              id="fahrenheit"
              name="temperature"
              value="fahrenheit"
              v-model="temperature"
            />
            <label for="fahrenheit">Fahrenheit (°F)</label>
          </div>
        </fieldset>
        <fieldset>
          <span>Wind Speed</span>

          <div>
            <input type="radio" id="kmh" name="windSpeed" value="kmh" v-model="windSpeed" />
            <label for="kmh">km/h</label>
          </div>

          <div>
            <input type="radio" id="mph" name="windSpeed" value="mph" v-model="windSpeed" />
            <label for="mph">mph</label>
          </div>
        </fieldset>
        <fieldset>
          <span>Precipitation</span>

          <div>
            <input type="radio" id="mm" name="precipitation" value="mm" v-model="precipitation" />
            <label for="mm">Millimeters (mm)</label>
          </div>

          <div>
            <input
              type="radio"
              id="inch"
              name="precipitation"
              value="inch"
              v-model="precipitation"
            />
            <label for="inch">Inches (in)</label>
          </div>
        </fieldset>
      </div>
    </div>
  </header>
</template>
