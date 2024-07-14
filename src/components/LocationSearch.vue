<template>
    <div>
      <input v-model="query" @input="searchLocation" placeholder="Enter location" />
      <ul>
        <li v-for="location in locations" :key="location.id" @click="selectLocation(location)">
          {{ location.name }}
        </li>
      </ul>
    </div>
  </template>
  
  <script>
  import axios from 'axios';
  
  export default {
    data() {
      return {
        query: '',
        locations: []
      };
    },
    methods: {
      async searchLocation() {
        if (this.query.length < 3) return;
        const response = await axios.get(`https://geocoding-api.open-meteo.com/v1/search?name=${this.query}&count=10&language=en&format=json`);
        this.locations = response.data.results;
      },
      selectLocation(location) {
        this.$emit('location-selected', location);
      }
    }
  };
  </script>
  