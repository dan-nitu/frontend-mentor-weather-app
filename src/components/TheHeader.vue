<script setup>
import { ref, defineEmits, onMounted, onUnmounted } from 'vue'

const emit = defineEmits()

const showOptions = ref(false)
const optionsRef = ref(null)

const imperialUnits = ref(false)
const measurementUnits = ref('metric')
const temperature = ref('celsius')
const windSpeed = ref('kmh')
const precipitation = ref('mm')

const toggleOptions = () => {
  showOptions.value = !showOptions.value
}

const toggleUnits = () => {
  imperialUnits.value = !imperialUnits.value

  temperature.value = imperialUnits.value ? 'fahrenheit' : 'celsius'
  windSpeed.value = imperialUnits.value ? 'mph' : 'kmh'
  precipitation.value = imperialUnits.value ? 'inch' : 'mm'
  measurementUnits.value = imperialUnits.value ? 'imperial' : 'metric'

  emit('measurementUnit', measurementUnits.value)
}

const handleClickOutside = (event) => {
  if (optionsRef.value && !optionsRef.value.contains(event.target)) {
    showOptions.value = false
  }
}

onMounted(() => {
  document.addEventListener('click', handleClickOutside)

  const handleKeydown = (e) => {
    if (e.key === 'Escape') showOptions.value = false
  }
  document.addEventListener('keydown', handleKeydown)
})

onUnmounted(() => {
  document.removeEventListener('click', handleClickOutside)
  document.removeEventListener('keydown', handleKeydown)
})
</script>

<template>
  <header>
    <a href="/">
      <img src="@/assets/images/logo.svg" alt="Logo" />
    </a>

    <div class="toggle-button-wrapper" ref="optionsRef">
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
              disabled
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
              disabled
            />
            <label for="fahrenheit">Fahrenheit (°F)</label>
          </div>
        </fieldset>
        <fieldset>
          <span>Wind Speed</span>

          <div>
            <input
              type="radio"
              id="kmh"
              name="windSpeed"
              value="kmh"
              v-model="windSpeed"
              disabled
            />
            <label for="kmh">km/h</label>
          </div>

          <div>
            <input
              type="radio"
              id="mph"
              name="windSpeed"
              value="mph"
              v-model="windSpeed"
              disabled
            />
            <label for="mph">mph</label>
          </div>
        </fieldset>
        <fieldset>
          <span>Precipitation</span>

          <div>
            <input
              type="radio"
              id="mm"
              name="precipitation"
              value="mm"
              v-model="precipitation"
              disabled
            />
            <label for="mm">Millimeters (mm)</label>
          </div>

          <div>
            <input
              type="radio"
              id="inch"
              name="precipitation"
              value="inch"
              v-model="precipitation"
              disabled
            />
            <label for="inch">Inches (in)</label>
          </div>
        </fieldset>
      </div>
    </div>
  </header>
</template>
