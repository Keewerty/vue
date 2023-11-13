<script setup>
import { ref } from 'vue';
import axios from 'axios';
import "bootstrap"

const weatherData = ref(null); // Use ref to create a reactive reference to weatherData
const forecastData = ref(null); // Use ref to create a reactive reference to weatherData

// Get the user's current location
if ("geolocation" in navigator) {
  navigator.geolocation.getCurrentPosition((position) => {
    const lat = position.coords.latitude;
    const lng = position.coords.longitude;
 
    const current = 'current.json';
    const forecast = 'forecast.json'
    const apiKey = 'b03dc26e06c940df9b034955230811';
    const query = `${lat},${lng}`;
    const BASE_URL = 'http://api.weatherapi.com/v1/'
    // const currentURL = `${BASE_URL}${current}?key=${apiKey}&q=${query}`;
    const forecastURL =`${BASE_URL}${forecast}?key=${apiKey}&q=${query}`;

      axios
      .get(forecastURL)
      .then((response) => {

        console.log('Forecast Data:', response.data);
        forecastData.value = response.data;
      })
      .catch((error) => {

        console.error('Error:', error);
      });


  }, (error) => {

    console.error('Geolocation Error:', error);
  });
} else {
  // Geolocation is not available in this browser
  console.error('Geolocation is not supported in this browser.');
}

const currentTime = ref(getCurrentTime());
const currentDate = ref(getCurrentDate());

function getCurrentTime() {
  const now = new Date();
  const hours = now.getHours().toString().padStart(2, '0');
  const minutes = now.getMinutes().toString().padStart(2, '0');
  return `${hours}:${minutes}`;
}

function getCurrentDate() {
  const now = new Date();
  const options = { weekday: 'long', day: '2-digit', month: 'short'};
  return now.toLocaleDateString("en-GB", options).replace(/\//g, ' ');
}

setInterval(() => {
  currentTime.value = getCurrentTime();
  currentDate.value = getCurrentDate();
}, 60000);
</script>


<template>
  <div v-if="forecastData">
    <p class="currentDate">{{ currentDate }}</p>

    <p class="currentTime">{{ currentTime }}</p>
    <h1 class="loc_name">{{ forecastData.location.name }}</h1>
    <h1 class="temp_c">{{ forecastData.current.temp_c }}</h1>
  </div>
  <div v-else>
    <p class="loading">Loading weather data...</p>
  </div>
</template>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Dosis:wght@400;600;800&display=swap');
.temp_c, .loc_name, .currentTime{
  color: white;
  font-family: 'Dosis', sans-serif;
  margin: 0;
  text-shadow: 2px 2px 3px rgba(0, 0, 0, 0.4);
}
.temp_c{
  font-weight: 400;
}
.loc_name{
  font-weight: 600;
  font-size: 4rem;
}
.loading{
  font-size: 5vw;
  color: black;
}
</style>
