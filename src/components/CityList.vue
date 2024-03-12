<template>
  <div v-for="city in savedCities" :key="city.id">
    <CityCard :city="city" @click="goToCityView(city)"/>
  </div>
  <p v-if="savedCities.length === 0">
    No locations added. To start tracking a location, search in the field above.
  </p>
</template>

<script setup>
import axios from "axios";
import { ref } from "vue"
import CityCard from "./CityCard.vue";
import { useRouter } from "vue-router";

const router =useRouter();

const API_key = "78c99234d064aefe08346fee3818d459"
const savedCities = ref([])
const getCities = async () => {
  if (localStorage.getItem("savedCities")) {
    savedCities.value = JSON.parse(localStorage.getItem("savedCities"))
  }
  const request = []
  savedCities.value.forEach(city => {
    request.push(
      axios.get(`https://api.openweathermap.org/data/2.5/weather?lat=${city.coords.lat}&lon=${city.coords.lng}&exclude={part}&appid=${API_key}&units=metric`)
    )
  })

  const weatherData = await Promise.all(request)
  weatherData.forEach((value, index) => {
    savedCities.value[index].weather = value.data
  })
  console.log(":::weatherData", savedCities.value)
}

const goToCityView = (city) => {
  router.push({
    name: "cityView",
    params: {
      state: city.state,
      city: city.city,
    },
    query: {
      lat: city.coords.lat,
      lng: city.coords.lng,
      id: city.id,
    }
  })
}

await getCities()
</script>

