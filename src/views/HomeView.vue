<script setup>
import { ref } from "vue"

const searchQuery = ref('')
const queryTimeout = ref(null)
const searchResults = ref(null)
const apiKey = import.meta.env.VITE_APP_API_KEY

const getSearchResults = () => {
  clearTimeout(queryTimeout.value)
  queryTimeout.value = setTimeout(async () => {
    if (searchQuery.value) {
      const response = await fetch(`http://api.openweathermap.org/geo/1.0/direct?q=${searchQuery.value}&limit=5&appid=${apiKey}`)
      searchResults.value = await response.json()
      console.log(searchResults.value)
      return;
    }
    searchResults.value = null
  }, 300)


}
</script>

<template>
  <main class="container text-white">
    <div class="pt-4 mb-8 relative">
      <input v-model="searchQuery" @input="getSearchResults" type="text" placeholder="Search for a city or a state"
        class="py-2 px-1 w-full bg-transparent border-b focus:border-weather-secondary focus:outline-none focus:shadow-[0px_1px__0_0_#004E71]">
        <ul v-if="searchResults" class="absolute bg-weather-secondary text-white w-full shadow-md py-2 px-1 top-[66px]">
          <li v-for="(searchResult, index) in searchResults" :key="index" class="py-2 cursor-pointer">
          {{ searchResult.name }}, {{ searchResult.state }}</li>
        </ul>
    </div>
  </main>
</template>
