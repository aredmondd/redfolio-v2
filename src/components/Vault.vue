<script setup>
import { ref, onMounted } from 'vue'
import axios from 'axios'

const raindrops = ref([])
const tags = ref([])
const error = ref(null)

// stephen ango's flexoki at 33% opacity :)
let colorKey = {
  site: 'rgba(175, 48, 41, 0.33)',
  ui: 'rgba(188, 82, 21, 0.33)',
  resource: 'rgba(173, 131, 1, 0.33)',
  code: 'rgba(102, 128, 11, 0.33)',
  app: 'rgba(36, 131, 123, 0.33)',
  writing: 'rgba(32, 94, 166, 0.33)',
  inspo: 'rgba(94, 64, 157, 0.33)',
  addon: 'rgba(160, 47, 111, 0.33)',
}

async function fetchRaindrops() {
  const API_URL = 'https://api.raindrop.io/rest/v1/raindrops/50543962'
  const API_URL_TAGS = 'https://api.raindrop.io/rest/v1/tags/50543962'
  const API_TOKEN = import.meta.env.VITE_RAINDROP_ACCESS_TOKEN

  // get raindrops
  try {
    const response = await axios.get(API_URL, {
      headers: {
        Authorization: `Bearer ${API_TOKEN}`,
      },
    })

    raindrops.value = response.data.items
  } catch (err) {
    console.error(err)
    error.value = 'Failed to fetch raindrops. Please try again.'
  }

  // get tags
  try {
    const response = await axios.get(API_URL_TAGS, {
      headers: {
        Authorization: `Bearer ${API_TOKEN}`,
      },
    })

    tags.value = response.data.items
  } catch (err) {
    console.error(err)
    error.value = 'Failed to fetch tags. Please try again.'
  }
}

function generateDate(date) {
  let month = date.substring(date.indexOf('-') + 1, date.indexOf('-', 5))
  let day = date.substring(date.indexOf('-', 5) + 1, date.indexOf('T'))
  let year = date.substring(0, date.indexOf('-'))

  return year + '-' + month + '-' + day
}

onMounted(() => {
  fetchRaindrops()
})
</script>

<template>
  <div>
    <div v-if="error" class="text-red-500">{{ error }}</div>

    <ul class="flex flex-col gap-1">
      <li v-for="raindrop in raindrops" :key="raindrop._id" class="flex items-center">
        <!-- Title -->
        <a
          v-bind:href="raindrop.link"
          target="_blank"
          class="hover:text-pink truncate font-medium mr-2 text-sm sm:text-md"
        >
          {{ raindrop.title }}
        </a>

        <!-- tags -->
        <div
          v-for="(tag, index) in raindrop.tags"
          :key="index"
          class="hidden sm:block px-2 rounded-full mr-2 text-xs text-black border-1"
          :style="{ backgroundColor: colorKey[tag] }"
        >
          #{{ tag }}
        </div>

        <!-- Dots -->
        <span class="flex-grow mx-2 border-b-2 border-dotted border-black border-opacity-50"></span>

        <!-- Date -->
        <span class="text-black font-mono font-light text-sm sm:text-md">
          {{ generateDate(raindrop.created) }}
        </span>
      </li>
    </ul>
  </div>
</template>
