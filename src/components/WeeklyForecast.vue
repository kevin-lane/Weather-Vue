<template>
  <div class="bg-amber-300 rounded-2xl w-80 h-80  mt-4">
    <table class="caption-top text-center">
      <caption class="text-2xl font-sans text-center mt-4">
        Weekly Forecast
      </caption>
      <thead>
      <tr>
        <th></th>
        <th class="font-medium py-2">Lowest</th>
        <th class="font-medium py-2">Highest</th>
      </tr>
      </thead>
        <tbody>
          <tr v-for="(temp, index) in temps" :key="index">
            <th>{{ temp.date }}</th>
            <td><p class="px-8">{{ temp.lowestTemperature }}&#8451</p></td>
            <td><p class="px-6">{{ temp.highestTemperature }}&#8451</p></td>
          </tr>
        </tbody>
    </table>
  </div>
</template>

<script setup lang="ts">
    defineProps<{
      temps: [{
        highestTemperature: number,
        lowestTemperature: number,
        date: string,
      }]
    }>()
</script>
