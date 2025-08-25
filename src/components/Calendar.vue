<script setup>
import { defineProps } from 'vue';

defineProps({
  initialDate: {
    type: String,
    default: null
  },
  language: {
    type: String,
    default: 'ru'
  }
})


</script>
<template>
  компонент календаря
</template>