<template>
  <div class="min-h-screen bg-gray-100 flex flex-col items-center p-4">
    <h1 class="text-3xl font-bold mb-6">Weather Forecast</h1>
    <SearchBar @location-selected="fetchWeather" />
    <WeatherDisplay :weather-data="weatherData" :is-loading="isLoading" :error="error" />
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import axios from 'axios'

interface LocationData {
  latitude: number
  longitude: number
  name: string
}

interface WeatherData {
  city: string
  hourly: {
    temperature_2m: number[]
    weathercode: number[]
    time: string[]
  }
}

const weatherData = ref<WeatherData | null>(null)
const isLoading = ref<boolean>(false)
const error = ref<string | null>(null)

const fetchWeather = async ({ latitude, longitude, name }: LocationData) => {
  isLoading.value = true
  error.value = null
  
  try {
    const today = new Date().toISOString().split('T')[0]
    const response = await axios.get('https://api.open-meteo.com/v1/forecast', {
      params: {
        latitude,
        longitude,
        hourly: 'temperature_2m,weathercode',
        start_date: today,
        end_date: today,
        timezone: 'auto',
      },
    })

    weatherData.value = {
      city: name,
      hourly: response.data.hourly,
    }
  } catch (err) {
    error.value = 'Failed to load weather data'
    console.error('Weather fetch error:', err)
  } finally {
    isLoading.value = false
  }
}
</script>