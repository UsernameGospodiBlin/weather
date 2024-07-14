<template>
  <div id="app">
    <LocationSearch @location-selected="locationSelected" />
    <WeatherDisplay v-if="selectedLocation" :location="selectedLocation" />
  </div>
</template>

<script>
import LocationSearch from './components/LocationSearch.vue';
import WeatherDisplay from './components/WeatherDisplay.vue';

export default {
  components: {
    LocationSearch,
    WeatherDisplay
  },
  data() {
    return {
      selectedLocation: null
    };
  },
  methods: {
    locationSelected(location) {
      this.selectedLocation = location;
    }
  }
};
</script>
