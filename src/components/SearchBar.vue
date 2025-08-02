<template>
  <div class="w-full max-w-md mb-6">
    <input
      v-model="query"
      @input="searchLocations"
      type="text"
      placeholder="Enter city name"
      class="w-full p-2 border rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"
    />
    <ul v-if="locations.length" class="mt-2 bg-white border rounded-md max-h-60 overflow-y-auto">
      <li
        v-for="location in locations"
        :key="location.id"
        @click="selectLocation(location)"
        class="p-2 hover:bg-gray-100 cursor-pointer"
      >
        {{ location.name }}, {{ formatAdmin(location) }} {{ location.country }}
      </li>
    </ul>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import axios from 'axios'

interface Location {
  id: number
  name: string
  latitude: number
  longitude: number
  country: string
  admin1?: string
  admin2?: string
  admin3?: string
  admin4?: string
}

interface LocationSelectedData {
  latitude: number
  longitude: number
  name: string
}

const emit = defineEmits<{
  'location-selected': [data: LocationSelectedData]
}>()

const query = ref<string>('')
const locations = ref<Location[]>([])

const searchLocations = async (): Promise<void> => {
  if (query.value.length < 2) {
    locations.value = []
    return
  }
  
  try {
    const response = await axios.get('https://geocoding-api.open-meteo.com/v1/search', {
      params: {
        name: query.value,
        count: 10,
        language: 'en',
        format: 'json',
      },
    })
    locations.value = response.data.results || []
  } catch (err) {
    console.error('Search error:', err)
    locations.value = []
  }
}

const selectLocation = (location: Location): void => {
  emit('location-selected', {
    latitude: location.latitude,
    longitude: location.longitude,
    name: location.name,
  })
  query.value = ''
  locations.value = []
}

const formatAdmin = (location: Location): string => {
  const adminLevels = [
    location.admin1,
    location.admin2,
    location.admin3,
    location.admin4,
  ].filter((admin): admin is string => Boolean(admin))
  
  return adminLevels.length ? adminLevels.join(', ') : ''
}
</script>