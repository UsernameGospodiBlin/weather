<template>
  <div class="location-search">
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

<style scoped>
.location-search {
  font-family: Arial, sans-serif;
  margin: 20px;
}

input {
  width: 100%;
  padding: 10px;
  margin-bottom: 10px;
  border: 1px solid #ccc;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  background: #f9f9f9;
  margin: 5px 0;
  padding: 10px;
  border: 1px solid #ddd;
  cursor: pointer;
}

li:hover {
  background: #e9e9e9;
}
</style>
