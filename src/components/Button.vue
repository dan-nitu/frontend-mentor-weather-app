<script setup>
import { defineProps } from 'vue'

defineProps({
  type: String,
  buttonText: String,
})
</script>

<template>
  <button :type="type">{{ buttonText }}</button>
</template>
