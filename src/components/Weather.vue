<script setup lang="ts">
  const props = defineProps<{
    temperature: number,
    highestTemperature: number,
    lowestTemperature: number,
    weatherConditions: string
  }>()
</script>

<template>
  <div class="bg-amber-300 rounded-2xl w-80 h-80 mt-4">
    <h3 class="text-2xl font-sans text-center mt-4">Current weather</h3>
    <p class="text-4xl font-sans font-semibold text-center mt-8">{{ props.temperature }} &#8451</p>
    <div class="font-mono text-center space-x-20 mt-8">
      <span class="text-center">L: {{ props.lowestTemperature }} &#8451</span>
      <span class="text-center">H: {{ props.highestTemperature }} &#8451</span>
    </div>
    <p class="text-2xl text-center mt-16">{{ props.weatherConditions }}</p>
  </div>
</template>
