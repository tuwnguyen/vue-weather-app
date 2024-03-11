<template>
  <div class="flex flex-col flex-1 items-center">
    <!-- Banner -->
    <div
      v-if="route.query.preview"
      class="text-white p-4 bg-weather-secondary w-full text-center"
    >
      <p>You are currently previewing this city, click the "+" icon to start tracking this city</p>
    </div>
    <!-- Weather overview  -->
    <div class="flex flex-col flex-1 items-center text-white py-12 ">
      <h1 class="text-4xl mb-2">{{ route.params.city }}</h1>
      <p class="text-sm  mb-12">
        {{ 
          new Date(weatherData.currentTime).toLocaleDateString(
            "en-us",
            {
              weekday: "short",
              day: "2-digit",
              month: "long",
            }
          )  
        }}
        {{ 
          new Date(weatherData.currentTime).toLocaleTimeString(
            "en-us",
            {
              timeStyle: "short"
            }
          )
        }}

      </p>
      <p class="text-8xl mb-8">
        {{ Math.round(weatherData.current.temp) }}&deg;
      </p>
      <p>
        Feels like
        {{ Math.round(weatherData.current.feels_like) }}&deg;
      </p>
      <p class="capitalize">
        {{ weatherData.current.weather[0].description }}
      </p>
      <img 
        class="w-[150px] h-auto"
        :src="`http://openweathermap.org/img/wn/${weatherData.current.weather[0].icon}@2x.png`" alt="">
    </div>
  </div>
</template>

<script setup>
import axios from 'axios'
import { useRoute } from "vue-router"

const route = useRoute()
const getWeatherData = async () => {
  const { lat, lng, preview } = route.query
  const API_key = "78c99234d064aefe08346fee3818d459"
  try {
    const weatherData = await axios.get(
      `https://api.openweathermap.org/data/3.0/onecall?lat=${lat}&lon=${lng}&appid=${API_key}&units=metric`)
    
    // cal current date & time
    const localOffset = new Date().getTimezoneOffset() * 60000
    const utc = weatherData.data.current.dt * 1000 + localOffset
    const vnTime = utc + 1000 + weatherData.data.timezone_offset + (7 * 3600000) // Adding 7 hours for UTC+7 (Vietnam Time)
    weatherData.data.currentTime = vnTime

    // cal hourly weather offset
    weatherData.data.hourly.forEach(hour => {
      const utc = hour.dt * 1000 + localOffset
      hour.currentTime = utc + 1000 * weatherData.data.timezone_offset
    })
    return weatherData.data
  } catch (error) {
    console.log(error)
  }
}

const weatherData = await getWeatherData()
console.log(weatherData)

</script>
