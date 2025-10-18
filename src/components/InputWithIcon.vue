<script setup>
import { defineProps, defineEmits } from 'vue'

defineProps({
  icon: String,
  id: { type: String, default: '' },
  type: { type: String, default: 'text' },
  placeholder: { type: String, default: '' },
  modelValue: { type: String, default: '' },
})

const emit = defineEmits(['update:modelValue'])
</script>

<template>
  <div class="input-with-icon">
    <label :for="id" :class="`icon ${icon}`"></label>
    <input
      :id="id"
      :type="type"
      :placeholder="placeholder"
      :value="modelValue"
      autocomplete="off"
      @input="$emit('update:modelValue', $event.target.value)"
    />
  </div>
</template>
