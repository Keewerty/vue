<script setup>
import { ref } from 'vue';
import axios from 'axios';
import "bootstrap"


const forecastData = ref(null); // Use ref to create a reactive reference to weatherData
const icon = ref(null)

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
    const forecastURL = `${BASE_URL}${forecast}?key=${apiKey}&q=${query}`;

    axios
      .get(forecastURL)
      .then((response) => {

        console.log('Forecast Data:', response.data);
        forecastData.value = response.data;

        icon.value = `http:${response.data.current.condition.icon}`
        console.log("Icon:", icon.value)
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
  const options = { weekday: 'long', day: '2-digit', month: 'short' };
  return now.toLocaleDateString("en-GB", options).replace(/\//g, ' ');
}


setInterval(() => {
  currentTime.value = getCurrentTime();
  currentDate.value = getCurrentDate();
}, 60000);
</script>

<template>
  <div v-if="forecastData">
    <div class="container">
      <p class="currentDate">{{ currentDate }}</p>

      <p class="currentTime">{{ currentTime }}</p>
      <h1 class="loc-name">{{ forecastData.location.name }}</h1>
      <img :src="icon" alt="">
      <h1 class="temp-c">{{ forecastData.current.temp_c }} °C</h1>
    </div>
    <div class="container">
      <div class="container">
        <div class="row forecast-row">
          <div class="col">
            <p class="forecast">{{ new Date(forecastData.forecast.forecastday[0].hour[0].time).toLocaleTimeString('en-GB',
              { hour: '2-digit', minute: '2-digit' }) }}</p>
            <p>
              {{ forecastData.forecast.forecastday[0].hour[0].temp_c }} °C
            </p>
          </div>
          <div class="col">
            <p class="forecast">{{ new Date(forecastData.forecast.forecastday[0].hour[6].time).toLocaleTimeString('en-GB',
              { hour: '2-digit', minute: '2-digit' }) }}</p>
            <p>
              {{ forecastData.forecast.forecastday[0].hour[6].temp_c }} °C
            </p>
          </div>
          <div class="col">
            <p class="forecast">{{ new
              Date(forecastData.forecast.forecastday[0].hour[12].time).toLocaleTimeString('en-GB',
                { hour: '2-digit', minute: '2-digit' }) }}</p>
            <p>
              {{ forecastData.forecast.forecastday[0].hour[12].temp_c }} °C
            </p>
          </div>
          <div class="col">
            <p class="forecast">{{ new
              Date(forecastData.forecast.forecastday[0].hour[15].time).toLocaleTimeString('en-GB',
                { hour: '2-digit', minute: '2-digit' }) }}</p>
            <p>
              {{ forecastData.forecast.forecastday[0].hour[15].temp_c }} °C
            </p>
          </div>
          <div class="col">
            <p class="forecast">{{ new
              Date(forecastData.forecast.forecastday[0].hour[21].time).toLocaleTimeString('en-GB',
                { hour: '2-digit', minute: '2-digit' }) }}</p>
            <p>
              {{ forecastData.forecast.forecastday[0].hour[21].temp_c }} °C
            </p>
          </div>
        </div>
      </div>

    </div>
  </div>
  <div v-else>
    <p class="loading">Loading weather data...</p>
  </div>
</template>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Dosis:wght@400;600;800&display=swap');

.temp-c,
.loc-name,
.currentTime,
.currentDate,
.forecast-row p {
  color: white;
  font-family: 'Dosis', sans-serif;
  margin: 0;
  text-shadow: 2px 2px 3px rgba(0, 0, 0, 0.4);
}

.currentDate {
  font-size: 1rem;
}

.currentTime {
  font-size: 3rem;
}

.forecast-row {
  display: flex;
  gap: 2rem;
  margin-top: 4vh;
}

.forecast-row p {
  font-size: 1rem;
}

.temp-c {
  font-weight: 400;
}

.loc-name {
  font-weight: 600;
  font-size: 6rem;
}

.loading {
  font-size: 5vw;
  color: black;
}
</style>
