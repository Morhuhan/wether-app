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

<script>
import axios from 'axios';

export default {
  name: 'SearchBar',
  data() {
    return {
      query: '',
      locations: [],
    };
  },
  methods: {
    async searchLocations() {
      if (this.query.length < 2) {
        this.locations = [];
        return;
      }
      try {
        const response = await axios.get('https://geocoding-api.open-meteo.com/v1/search', {
          params: {
            name: this.query,
            count: 10,
            language: 'en',
            format: 'json',
          },
        });
        this.locations = response.data.results || [];
      } catch (err) {
        console.error('Search error:', err);
      }
    },
    selectLocation(location) {
      this.$emit('location-selected', {
        latitude: location.latitude,
        longitude: location.longitude,
        name: location.name,
      });
      this.query = '';
      this.locations = [];
    },
    formatAdmin(location) {
      const adminLevels = [
        location.admin1,
        location.admin2,
        location.admin3,
        location.admin4,
      ].filter((admin) => admin);
      return adminLevels.length ? adminLevels.join(', ') : '';
    },
  },
};
</script>