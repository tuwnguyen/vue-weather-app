<template>
  <div>

  </div>
</template>

<script setup>
import axios from 'axios'
import { useRoute } from "vue-router"

const route = useRoute()
const getWeatherData = async () => {
  const { lat, lng, preview } = route.query
  const API_key = "b24e170ce85a523c8ab6eca313b2f9cd"
  try {
    const weatherData = await axios.get(`https://api.openweathermap.org/data/2.5/onecall?lat=${lat}&lon=${lng}&exclude={part}&appid=${API_key}&units=imperial`)
    
    // cal current date & time
    const localOffset = new Date().getTimezoneOffset() * 60000
    const utc = weatherData.data.current.dt * 1000 + localOffset
    weatherData.data.currentTime = utc + 1000 + weatherData.data.timezone_offset

    // cal hourly weather offset
    weatherData.data.hourly.forEach(hour => {
      const utc = hour.dt * 1000 + localOffset
      hour.currentTime = utc + 1000 * weatherData.data.timezone_offset
    })
    return weatherData
  } catch (error) {
    console.log(error)
  }
}

const weatherData = await getWeatherData()
console.log(weatherData)

</script>
