<template>
  <div>
    <Suspense>
      <AsyncCityView/>
      <template #fallback>
          <CityViewSkeleton />
      </template>
    </Suspense>
  </div>
</template>

<script setup>
import AsyncCityView from './AsyncCityView.vue';
import CityViewSkeleton from '../components/CityViewSkeleton.vue';
</script>
