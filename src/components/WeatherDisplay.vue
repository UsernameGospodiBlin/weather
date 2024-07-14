<template>
  <div class="weather-display">
    <h2>{{ location.name }}</h2>
    <div v-if="weather">
      <div class="current-weather">
        <h3>Current weather</h3>
        <p>Temperature: {{ weather.current.temperature }}°C</p>
        <p>condition: {{ weather.current.condition }}</p>
      </div>
      <div class="forecast">
        <h3>forecast</h3>
        <ul>
          <li v-for="day in weather.forecast" :key="day.date">
            {{ day.date }}: {{ day.temperature }}°C
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';
import moment from 'moment';
import weatherCodeMapping from '../utils/weatherCodes.js';

export default {
  props: {
    location: Object
  },
  data() {
    return {
      weather: null
    };
  },
  watch: {
    location: {
      immediate: true,
      handler(newLocation) {
        if (newLocation) {
          this.fetchWeather(newLocation);
        }
      }
    }
  },
  methods: {
    async fetchWeather(location) {
      const startDate = moment().format('YYYY-MM-DD');
      const endDate = moment().add(1, 'days').format('YYYY-MM-DD');
      const forecastUrl = `https://api.open-meteo.com/v1/forecast?latitude=${location.latitude}&longitude=${location.longitude}&hourly=temperature_2m&start_date=${startDate}&end_date=${endDate}&current_weather=true`;

      const response = await axios.get(forecastUrl);
      this.weather = {
        current: {
          temperature: response.data.current_weather.temperature,
          condition: weatherCodeMapping[response.data.current_weather.weathercode] || "Неизвестно"
        },
        forecast: response.data.hourly.temperature_2m.map((temp, index) => ({
          date: moment().add(index, 'hours').format('YYYY-MM-DD HH:mm'),
          temperature: temp
        }))
      };
    }
  }
};
</script>

<style scoped>
.weather-display {
  font-family: Arial, sans-serif;
  margin: 20px;
}

.current-weather, .forecast {
  border: 1px solid #ccc;
  padding: 10px;
  margin-top: 10px;
}

.current-weather h3, .forecast h3 {
  margin-top: 0;
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
}
</style>
