<script setup>
import { ref } from 'vue';
import axios from 'axios';
import { useRouter } from 'vue-router';


const router = useRouter()
const mapboxAPIKey = 'pk.eyJ1IjoidHV3bmd1eWVuIiwiYSI6ImNsdGZtdjdhMDByd24yam8zbXM3YnN5dGMifQ.pm0fxewcKyC1dYtfKhd-Dg'
const searchQuery = ref('')
const queryTimeout = ref(null)
const mapboxSearchResults = ref(null)
const searchError = ref(null)

const getSearchResults = () => {
  clearTimeout(queryTimeout);
  queryTimeout.value = setTimeout(async () => {
    if (searchQuery !== "") {
      try {
        const result = await axios.get(`https://api.mapbox.com/geocoding/v5/mapbox.places/${searchQuery.value}.json?access_token=${mapboxAPIKey}&types=place`)
        mapboxSearchResults.value = result.data.features
      } catch (error) {
        searchError.value = true
      }
      return
    }
  }, 300)
  mapboxSearchResults.value = null
}

const previewCity = (searchResult) => {
  console.log(searchResult)
  const [city, state] = searchResult.place_name.split(",")
  router.push({
    name: "cityView",
    params: {city: city, state: state.replaceAll(" ", "")},// del space in params in url
    query: {
      lat: searchResult.geometry.coordinates[1],
      lng: searchResult.geometry.coordinates[0],
      preview: true,
    }
  })
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
        <p v-if="searchError" class="py-2">
          Sorry, something went wrong, please try again
        </p>
        <p v-if="!searchError && mapboxSearchResults.length === 0" class="py-2">
          No results match your query, trye a different term
        </p>
        <template v-else>
          <li
            v-for="searchResult in mapboxSearchResults" :key="searchResult.id"
            class="py-2 cursor-pointer"
            @click="previewCity(searchResult)"
          >
            {{ searchResult.place_name }}
          </li>
        </template>
      </ul>
    </div>
  </main>
</template>