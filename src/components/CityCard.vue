<template>
  <div class="flex py-6 bg-weather-secondary rounded-md shadow-md cursor-pointer">
    <div class="flex flex-col flex-1 px-2">
      <h2 class="text-3xl">{{ city.city }}</h2>
      <h3>{{ city.state }}</h3>
    </div>
    <div class="flex flex-col gap-2 px-2">
      <p class="text-3xl self-end">
        {{ Math.round(city.weather.main.temp) }}&deg;
      </p>
      <div class="flex gap-2">
        <span class="text-xs">
          H:
          {{ Math.round(city.weather.main.temp_max)}}&deg;
        </span>
        <span class="text-xs">
          H:
          {{ Math.round(city.weather.main.temp_min)}}&deg;
        </span>
      </div>
    </div>
  </div>
</template>

<script setup>
defineProps({
  city: {
    type: Object,
    default: () => {}
  }
})
// console.log(':::', defin)
</script>

