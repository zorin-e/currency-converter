<template>
  <CurrencyConverter
    v-model="value"
    :rate="1.08"
    :bonus-percent="27"
  />
</template>

<script setup lang="ts">
import CurrencyConverter from './components/CurrencyConverter.vue'
import { ref } from 'vue';

const value = ref(100)
</script>
