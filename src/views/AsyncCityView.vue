<template>
  <div class="flex flex-col flex-1 items-center">
    <!-- Banner -->
    <div
      v-if="route.query.preview"
      class="text-white p-4 bg-gradient-to-r from-sky-500 to-indigo-500 w-full text-center"
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
        :src="`http://openweathermap.org/img/wn/${weatherData.current.weather[0].icon}@2x.png`" alt=""
      >
    </div>
    
    <hr class="border-white border-opacity-10 border w-full"/>
    
    <!-- Hourly Weather -->
    <div class="max-w-screen-md w-full py-12">
      <div class="mx-8  text-white ">
        <h2 class="mb-4"> Hourly Weather</h2>
        <div class="flex gap-10 overflow-x-scroll">
          <div
            class="flex flex-col gap-4 items-center"
            v-for="hourData in weatherData.hourly"
            :key="hourData.dt"
          >
            <p class="whitespace-nowrap text-md">
              {{ 
                new Date(hourData.currentTime).toLocaleTimeString(
                  "en-us",
                  {hour: "numeric"}
                )
              }}
            </p>
            <img 
              class="w-auto h-[50px] object-cover"
              :src="`http://openweathermap.org/img/wn/${hourData.weather[0].icon}@2x.png`"
              alt=""
            >
            <p class="text-xl">
              {{ Math.round(hourData.temp) }}&deg;
            </p>
          </div>
        </div>
      </div>
    </div>

    <hr class="border-white border-opacity-10 border w-full"/>
    
    <!-- Weekly Weather -->
    <div class="max-w-screen-md w-full py-12">
      <div class="mx-8  text-white ">
        <h2 class="mb-4"> 7-Day Forecast</h2>
          <div
            class="flex items-center"
            v-for="day in weatherData.daily"
            :key="day.dt"
          >
            <p class="flex-1">
              {{ 
                new Date(day.dt * 1000).toLocaleDateString(
                  "en-us",
                  {weekday: "long"}
                )
              }}
            </p>
            <img 
              class="w-[50px] h-[50px] object-cover"
              :src="`http://openweathermap.org/img/wn/${day.weather[0].icon}@2x.png`"
            >
            <div class="flex gap-2 flex-1 justify-end">
              <p>H: {{ Math.round(day.temp.max) }}&deg;</p>
              <p>L: {{ Math.round(day.temp.min) }}&deg;</p>
            </div>
          </div>
      </div>
    </div>

    <div 
      class="flex items-center gap-2 py-12 text-white cursor-pointer duration-150 hover:text-red-500"
      @click="removeCity"
    >
      <i class="fa-solid fa-trash"></i>
      <p>Remove City</p>
    </div>
  </div>
</template>

<script setup>
import axios from 'axios'
import { useRoute, useRouter } from "vue-router"

const route = useRoute()
const router = useRouter()
const getWeatherData = async () => {
  const { lat, lng, preview } = route.query
  const API_key = "78c99234d064aefe08346fee3818d459"
  try {
    const weatherData = await axios.get(
      `https://api.openweathermap.org/data/3.0/onecall?lat=${lat}&lon=${lng}&appid=${API_key}&units=metric`)
    
    //flicker delay
    await new Promise(res => setTimeout(res, 1000))

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

const removeCity = () => {
  const cities = JSON.parse(localStorage.getItem("savedCities"))
  const updatedCities = cities.filter(c => c.id !== route.query.id)
  console.log(cities, updatedCities)
  localStorage.setItem(
    "savedCities",
    JSON.stringify(updatedCities)
  )
  router.push({
    name: 'home',
  })
}
</script>
