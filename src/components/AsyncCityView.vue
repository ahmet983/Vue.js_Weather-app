<template>
  <div class="flex flex-col flex-1 items-center">
    <!-- Banner -->
    <div v-if="route.query.preview" class="text-white p-4 bg-weather-secondary w-full text-center">
      <p>You are currently previewing this city, click the "+" icon to start tracking this city.</p>
    </div>
  <!-- Weather Overview -->
    <div v-if="weatherData" class="flex flex-col items-center text-white py-12">
      <h1 class="text-4xl mb-2">{{ route.params.city }}</h1>
      <p class="text-sm mb-12">{{ dateBuilder() }}</p>
      <p class="text-8xl mb-8">{{ Math.round(weatherData.main.temp)}}°C</p>
      <p>Feels Like {{ Math.round(weatherData.main.feels_like)}}°C</p>
      <p class="capitalize">{{ weatherData.weather[0].description}}</p>
      <img class="w-[150px] h-auto" :src="`http://openweathermap.org/img/wn/${weatherData.weather[0].icon}@2x.png`" alt="">
    </div>

    <hr class="border-white border-opacity-10 border w-full" />

<!-- Remove City -->
    <div @click="removeCity" class="flex items-center gap-2 py-12 text-white cursor-pointer duration-150 hover:text-red-500">
      <i class="fa-solid fa-trash"></i>
      <p>Remove City</p>
    </div>
    <div v-if="error1">
      saasfasfas
    </div>
  </div>
</template>

<script setup>
import axios from "axios";
import { useRoute, useRouter } from "vue-router";;
  
  const router = useRouter()
  const route = useRoute()
  let place = localStorage.place_name
  const getWeatherData = async () => {
      try {
          // const weatherData = await axios.get(`https://api.openweathermap.org/data/3.0/onecall?lat=${route.query.lat}&lon=${route.query.lng}&exclude={part}&appid=8d5a87e2f979feb3b4c059876c5d71c7&units=imperial`)
          const weatherData = await axios.get(`https://api.openweathermap.org/data/2.5/weather?q=${place}&units=metric&APPID=8d5a87e2f979feb3b4c059876c5d71c7`)
          console.log(weatherData);

          await new Promise((res) => setTimeout(res, 600));

          return weatherData.data;
      } 
      catch (err) {
        console.log(err.response.data.cod);

        alert("şehir bulunamadı")
        if(err.response.data.cod == 404)
        {
          router.push({ name: 'home',})
        }

      }
      
  };


  
  const weatherData = await getWeatherData();
  
  const removeCity = () => {
    const cities = JSON.parse(localStorage.getItem('savedCities'))
    const updatedCities = cities.filter((city) => city.id !== route.query.id)
    localStorage.setItem("savedCities", JSON.stringify(updatedCities))
    router.push({
      name: "home"
    })
}
</script>

<script>
  export default {
      methods:{
          dateBuilder () {
              let d = new Date();
              let months = ["January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December"];
              let days = ["Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"];

              let day = days[d.getDay()];
              let date = d.getDate();
              let month = months[d.getMonth()];

              let year = d.getFullYear();
              return `${day} ${date} ${month} ${year}`;
          },
      }
  };
</script>
