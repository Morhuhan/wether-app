<template>
  <div class="w-full max-w-6xl">
    <div v-if="isLoading" class="text-center text-xl">Загрузка...</div>
    <div v-else-if="error" class="text-red-500 text-center text-xl">{{ error }}</div>
    <div v-else-if="weatherData" class="p-6 bg-white rounded-lg shadow-lg">
      <h2 class="text-3xl font-semibold mb-6 text-center">{{ weatherData.city }}</h2>
      <div class="grid grid-cols-[repeat(auto-fit,minmax(80px,1fr))] gap-2">
        <div
          v-for="(temp, index) in weatherData.hourly.temperature_2m"
          :key="index"
          class="flex flex-col items-center"
        >
          <span class="text-lg font-medium">{{ temp }}°C</span>
          <span class="text-2xl my-2">{{ getWeatherIcon(weatherData.hourly.weathercode[index]) }}</span>
          <span class="text-sm">{{ formatTime(weatherData.hourly.time[index]) }}</span>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'WeatherDisplay',
  props: {
    weatherData: Object,
    isLoading: Boolean,
    error: String,
  },
  methods: {
    getWeatherIcon(code) {
      const codes = {
        0: '☀️',   // Clear sky
        1: '🌤️',   // Mainly clear
        2: '⛅',   // Partly cloudy
        3: '☁️',   // Overcast
        45: '🌫️',  // Fog
        48: '🌫️',  // Depositing rime fog
        51: '🌦️',  // Drizzle: Light
        53: '🌦️',  // Drizzle: moderate
        55: '🌧️',  // Drizzle: dense intensity
        56: '🌨️',  // Freezing Drizzle: Light
        57: '🌨️',  // Freezing Drizzle: dense intensity
        61: '🌧️',  // Rain: Slight
        63: '🌧️',  // Rain: moderate
        65: '🌧️',  // Rain: heavy intensity
        66: '🌨️',  // Freezing Rain: Light
        67: '🌨️',  // Freezing Rain: heavy intensity
        71: '❄️',  // Snow fall: Slight
        73: '🌨️',  // Snow fall: moderate
        75: '🌨️',  // Snow fall: heavy intensity
        77: '❄️',  // Snow grains
        80: '🌦️',  // Rain showers: Slight
        81: '🌧️',  // Rain showers: moderate
        82: '⛈️',  // Rain showers: violent
        85: '🌨️',  // Snow showers: slight
        86: '🌨️',  // Snow showers: heavy
        95: '⛈️',  // Thunderstorm: Slight or moderate
        96: '⛈️',  // Thunderstorm with slight hail
        99: '⛈️',  // Thunderstorm with heavy hail
      };
      return codes[code];
    },
    formatTime(isoTime) {
      return new Date(isoTime).toLocaleTimeString('ru-RU', {
        hour: '2-digit',
        minute: '2-digit',
      });
    },
  },
};
</script>