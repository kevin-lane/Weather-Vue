<script setup lang="ts">
  const emit = defineEmits(['searchCity', 'update:searchQuery', 'inputChange']);
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
    <input class="border bg-white w-96 h-12 pl-4 border-black rounded-l-lg border-r-white" :value="searchQuery" @input="updateSearchQuery" v-on:keyup.delete="$emit('inputChange')" type="text" placeholder="Check weather in a city, e.g. London">
    <button class="border w-28 bg-black text-white border-black rounded-r-lg border-l-white" @click="$emit('searchCity')">Search</button>
  </div>
</template>
