<template>
  <div class="bg-amber-300 rounded-2xl w-80 h-80">
    <h3 class="text-2xl font-sans text-center mt-4">Local Time</h3>
    <p class="text-4xl font-sans text-center mt-8">{{$props.year + '-' + $props.month + '-' + $props.day + ' ' + $props.dayOfWeek}}</p>
    <p class="text-4xl font-semibold  text-center mt-8">{{ $props.time }}</p>
  </div>
</template>

<script setup lang="ts">
    defineProps<{
      year: number,
      month: number,
      day: number,
      dayOfWeek:string,
      time: string
    }>();
</script>

<style scoped>

</style>
