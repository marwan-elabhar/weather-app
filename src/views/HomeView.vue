<script setup>
import { ref } from "vue"
import { useRouter } from "vue-router";

const searchQuery = ref('')
const queryTimeout = ref(null)
const searchResults = ref(null)
const searchError = ref(null)
const apiKey = import.meta.env.VITE_APP_API_KEY

const router = useRouter()

const previewCity = (searchResult) => {
  router.push({
    name: 'cityView',
    params: {
      state: searchResult.state,
      city: searchResult.name
    },
    query: {
      lat: searchResult.lat,
      lon: searchResult.lon
    }
  })
}

const getSearchResults = () => {
  clearTimeout(queryTimeout.value)
  queryTimeout.value = setTimeout(async () => {
    if (searchQuery.value) {
      try {
        const response = await fetch(`http://api.openweathermap.org/geo/1.0/direct?q=${searchQuery.value}&limit=5&appid=${apiKey}`)
        searchResults.value = await response.json()
      } catch {
        searchError.value = true
      }

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
        <p v-if="searchError">Sorry, something went wrong, please try again</p>
        <p v-if="!searchError && !searchResults.length">No results match your query, try a different term.</p>
        <template v-else>
          <li v-for="(searchResult, index) in searchResults" :key="index" @click="previewCity(searchResult)" class="py-2 cursor-pointer">
            {{ searchResult.name }}, {{ searchResult.state }}</li>
        </template>

      </ul>
    </div>
  </main>
</template>
