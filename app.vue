<script setup lang="ts">
import { useTimeAgo, useDateFormat } from "@vueuse/core"
import { startOfDay } from "date-fns"

enum District {
  blackriver,
  flacq,
  grandport,
  moka,
  pamplemousses,
  plainewilhems,
  portlouis,
  rivieredurempart,
  savanne,
  rodrigues,
}

interface Record {
  date: string
  locality: string
  streets: string
  district: District
  from: Date
  to: Date
  id: string
}

const API_ENDPOINT =
  "https://raw.githubusercontent.com/MrSunshyne/mauritius-dataset-electricity/main/data/power-outages.latest.json"

async function fetchJson(url = API_ENDPOINT) {
  try {
    const response = await fetch(url)
    return response.json()
  } catch (error) {
    throw new Error(`Error fetching JSON: ${error}`)
  }
}

const data = await fetchJson(API_ENDPOINT)

const { today, future } = data

console.log(today)

function getState(item: Record) {
  let on = "upcoming"
  const from = new Date(item.from)
  const to = new Date(item.to)
  const now = new Date()
  if (now.getTime() > from.getTime()) {
    on = "ongoing"
  }
  if (now.getTime() > to.getTime()) {
    on = "past"
  }
  return on
}

// todo: use code below to handle ongoing and upcoming outages

// function getTimeDifference(item: Record) {
//   let target

//   if (getState(item) === "ongoing") {
//     target = new Date(item.to)
//   } else if (getState(item) === "upcoming") {
//     target = new Date(item.from)
//   } else {
//     target = new Date(item.to)
//   }

//   const now = new Date()
//   return Math.abs(target.getTime() - now.getTime())
// }

// function formatDate(date: Date) {
//   let formatted = useDateFormat(date, "HH:mm")
//   return formatted.value.replaceAll('""', "")
// }
</script>
<template>
  <h1 class="text-white text-center text-4xl font-black leading-loose">
    Power Outages in Mauritius
  </h1>
  <div class="grid gap-y-12 max-w-screen-md mx-auto my-12">
    <div
      v-for="(outage, index) in today"
      :key="index"
      class="flex flex-col justify-between bg-blue-950 text-white p-4 rounded-md w-full"
    >
      <div class="flex justify-between w-full">
        <div class="text-xl font-bold">{{ outage.locality }}</div>
        <div class="w-6">
          <IconCandle />
        </div>
      </div>

      <div class="flex justify-between items-end">
        <div class="space-y-2">
          <p class="font-light max-w-3xl">
            {{ outage.streets }}
          </p>
          <div class="text-sm">
            {{ outage.date }}
          </div>
        </div>

        <div class="inline md:block">
          Power
          {{
            getState(outage) === "ongoing"
              ? "will resume in"
              : getState(outage) === "upcoming"
              ? "will cut in"
              : "has resumed"
          }}
        </div>

        <div
          class="inline md:block"
          v-if="!['ongoing', 'upcoming'].includes(getState(outage))"
        >
          {{ useTimeAgo(outage.from, { showSecond: true }) }}
        </div>
        <div class="inline md:block" v-else>
          todo: handle the time difference
        </div>
      </div>
    </div>
  </div>
</template>

<style lang="css">
body {
  background: linear-gradient(to right, #011458, #034c96);
}
</style>
