<template>
  <div class="min-h-screen bg-gray-100 flex flex-col items-center p-4">
    <h1 class="text-3xl font-bold mb-6">Weather Forecast</h1>
    <SearchBar @location-selected="fetchWeather" />
    <WeatherDisplay :weather-data="weatherData" :is-loading="isLoading" :error="error" />
  </div>
</template>

<script>
import SearchBar from './components/SearchBar.vue';
import WeatherDisplay from './components/WeatherDisplay.vue';
import axios from 'axios';

export default {
  name: 'App',
  components: {
    SearchBar,
    WeatherDisplay,
  },
  data() {
    return {
      weatherData: null,
      isLoading: false,
      error: null,
    };
  },
  methods: {
    async fetchWeather({ latitude, longitude, name }) {
      this.isLoading = true;
      this.error = null;
      try {
        const today = new Date().toISOString().split('T')[0];
        const response = await axios.get('https://api.open-meteo.com/v1/forecast', {
          params: {
            latitude,
            longitude,
            hourly: 'temperature_2m,weathercode',
            start_date: today,
            end_date: today,
            timezone: 'auto',
          },
        });

        this.weatherData = {
          city: name,
          hourly: response.data.hourly,
        };
      } catch (err) {
        this.error = 'Failed to load weather data';
      } finally {
        this.isLoading = false;
      }
    },
  },
};
</script>