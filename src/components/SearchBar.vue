<script setup lang="ts">
  const emit = defineEmits(['searchCity', 'update:searchQuery']);
  defineProps<{
    searchQuery: string
  }>();

  const updateSearchQuery = (event:Event) => {
    const target = event.target as HTMLInputElement;
    emit('update:searchQuery', target.value)
  }
</script>

<template>
  <div class="flex mt-6 mb-12 w-4/5 m-auto place-content-center">
    <input class="border w-96" :value="searchQuery" @input="updateSearchQuery" type="text" placeholder="Check weather in a city, e.g. London">
    <button class="border w-16" @click="$emit('searchCity')">Search</button>
  </div>
</template>
