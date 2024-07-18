<script setup lang="ts">
// todo: move to .env + Nuxt Config
const API_ENDPOINT =
  "https://raw.githubusercontent.com/MrSunshyne/mauritius-dataset-electricity/main/data/power-outages.latest.json"

// todo: explain & use useFetch
async function fetchJson(url = API_ENDPOINT) {
  try {
    const response = await fetch(url)
    return response.json()
  } catch (error) {
    throw new Error(`Error fetching JSON: ${error}`)
  }
}
// todo: remove when using useFetch as will be defined there
const data = await fetchJson(API_ENDPOINT)

const { today, future } = data
</script>

<template>
  <h1 class="text-white text-center text-4xl font-black leading-loose">
    Power Outages in Mauritius
  </h1>

  <div class="max-w-screen-md mx-auto my-12">
    <h2 class="text-white text-3xl font-black leading-relaxed my-2">Today</h2>
    <ul class="grid gap-y-12 max-w-screen-md mx-auto">
      <li v-for="(outage, index) in today" :key="index">
        <Outage :outage="outage" />
      </li>
    </ul>
  </div>

  <div class="max-w-screen-md mx-auto my-12">
    <h2 class="text-white text-3xl font-black leading-relaxed my-2">Future</h2>
    <ul class="grid gap-y-12">
      <li v-for="(outage, index) in future" :key="index">
        <Outage :outage="outage" />
      </li>
    </ul>
  </div>
</template>

<style lang="css">
body {
  background: linear-gradient(to right, #011458, #034c96);
}
</style>
