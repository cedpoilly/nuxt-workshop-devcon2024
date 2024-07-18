<script lang="ts" setup>
import { useTimeAgo } from "@vueuse/core"

// todo: move to types.ts (?)
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

// todo: move to types.ts (?)
interface Record {
  date: string
  locality: string
  streets: string
  district: District
  from: Date
  to: Date
  id: string
}

type Props = {
  outage: Record
}

const props = defineProps<Props>()

const state = computed(() => {
  let on = "upcoming"

  const from = new Date(props.outage.from)
  const to = new Date(props.outage.to)

  const now = new Date()

  if (now.getTime() > from.getTime()) {
    on = "ongoing"
  }

  if (now.getTime() > to.getTime()) {
    on = "past"
  }

  return on
})

const outageMessage = computed(() => {
  if (state.value === "ongoing") {
    return "will resume in"
  } else if (state.value === "upcoming") {
    return "will cut in"
  } else {
    return "has resumed"
  }
})
</script>

<template>
  <div
    class="flex flex-col justify-between bg-blue-950 text-white p-4 rounded-md w-full"
  >
    <div class="flex justify-between w-full">
      <div class="text-xl font-bold">{{ props.outage.locality }}</div>
      <div class="w-6">
        <!-- todo: have the candle <IconCandle /> -->
        🕯️
      </div>
    </div>

    <div class="flex justify-between items-end">
      <div class="space-y-2">
        <p class="font-light max-w-3xl">
          {{ props.outage.streets }}
        </p>
        <div class="text-sm">
          {{ props.outage.date }}
        </div>
      </div>

      <div class="inline md:block">Power {{ outageMessage }}</div>

      <div
        class="inline md:block"
        v-if="!['ongoing', 'upcoming'].includes(state)"
      >
        {{ useTimeAgo(outage.from, { showSecond: true }) }}
      </div>
      <div class="inline md:block" v-else>
        {{ useTimeAgo(outage.from) }}
      </div>
    </div>
  </div>
</template>
