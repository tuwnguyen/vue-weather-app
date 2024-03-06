<script setup>
import { ref } from 'vue';
import axios from 'axios';

const mapboxAPIKey = 'pk.eyJ1IjoidHV3bmd1eWVuIiwiYSI6ImNsdGZtdjdhMDByd24yam8zbXM3YnN5dGMifQ.pm0fxewcKyC1dYtfKhd-Dg'
const searchQuery = ref('')
const queryTimeout = ref(null)
const mapboxSearchResults = ref(null)

const getSearchResults = () => {
  clearTimeout(queryTimeout);
  queryTimeout.value = setTimeout(async () => {
    const result = await axios.get(`https://api.mapbox.com/geocoding/v5/mapbox.places/${searchQuery.value}.json?access_token=${mapboxAPIKey}&types=place`)
    mapboxSearchResults.value = result.data.features
    console.log(':::result', mapboxSearchResults.value)
  }, 300)
  mapboxSearchResults.value = null
}


</script>

<template>
  <main class="container text-white">
    <div class="pt-4  mb-8 relative">
      <input
        v-model="searchQuery"
        @input="getSearchResults"
        type="text"
        placeholder="Search for a city or state"
        class="py-2 px-1 w-full bg-transparent border-b 
        focus:border-weather-secondary focus:outline-none focus:shadow=[0px_1px_0_0#004E71]"
      />
      <ul 
        v-if="mapboxSearchResults"
        class="absolute bg-weather-secondary text-white w-full shadow-md py-2 px-1 top-[66px]">
        <li 
          v-for="searchResult in mapboxSearchResults" :key="searchResult.id"
          class="py-2 cursor-pointer"
        >
          {{ searchResult.place_name }}
        </li>
      </ul>
    </div>
  </main>
</template>