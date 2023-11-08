<script setup>
import axios from 'axios';
import "bootstrap"

var weatherData = null
// Get the user's current location
if ("geolocation" in navigator) {
  navigator.geolocation.getCurrentPosition((position) => {
    const lat = position.coords.latitude;
    const lng = position.coords.longitude;
 
    // Use lat and lng to build your WeatherAPI request
    const endpoint = 'current.json';
    const apiKey = 'b03dc26e06c940df9b034955230811';
    const query = `${lat},${lng}`;
    const BASE_URL = `http://api.weatherapi.com/v1/${endpoint}?key=${apiKey}&q=${query}`;

    axios
      .get(BASE_URL)
      .then((response) => {
        // Handle the response data here
        console.log('Weather Data:', response.data);
        weatherData = response.data
      })
      .catch((error) => {
        // Handle any errors
        console.error('Error:', error);
      });
  }, (error) => {
    // Handle geolocation error
    console.error('Geolocation Error:', error);
  });
} else {
  // Geolocation is not available in this browser
  console.error('Geolocation is not supported in this browser.');
}
</script>

<!-- Rest of your template and styles -->


<template>
  <h1 class="loc_name">{{ weatherData.location.name }}</h1>
  <h1 class="temp_c">{{ weatherData.current.temp_c }}</h1>
</template>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Dosis:wght@400;600;800&display=swap');
.temp_c, .loc_name{
  color: black;
  font-family: 'Dosis', sans-serif;
  margin: 0;
}
.temp_c{
  font-weight: 400;
}
.loc_name{
  font-weight: 600;
  font-size: 4rem;
}
</style>
