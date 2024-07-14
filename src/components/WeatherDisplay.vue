<template>
    <div>
      <h2>{{ location.name }}</h2>
      <div v-if="weather">
        <h3>Current Weather</h3>
        <p>Temperature: {{ weather.current.temperature }}°C</p>
        <p>Condition: {{ weather.current.condition }}</p>
        <h3>Forecast</h3>
        <ul>
          <li v-for="day in weather.forecast" :key="day.date">
            {{ day.date }}: {{ day.temperature }}°C
          </li>
        </ul>
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
            condition: weatherCodeMapping[response.data.current_weather.weathercode] || "Unknown"
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
  