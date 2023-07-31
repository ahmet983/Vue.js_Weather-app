<template>
  <div v-for="city in savedCities" :key="city.id">
    <CityCard :city="city" @click="goToCityView(city)" />
  </div>

  <p v-if="savedCities.length === 0">
    No locations added. To start tracking a location, search in the field above.
  </p>
</template>

<script setup>
import axios from "axios"
import { ref } from "vue";
import { useRouter } from "vue-router";
import CityCard from "./CityCard.vue";

let place = localStorage.place_name;
const savedCities = ref([]);
const getCities = async () => {
  if (localStorage.getItem("savedCities")) {
    savedCities.value = JSON.parse(
      localStorage.getItem("savedCities")
    );

    const requests = [];
    console.log(savedCities.value)
    savedCities.value.forEach((item) => {
      requests.push(
        axios.get(`https://api.openweathermap.org/data/2.5/weather?q=${item.city}&units=metric&APPID=8d5a87e2f979feb3b4c059876c5d71c7`)
        )
    })



    const weatherData = await Promise.all(requests);

    await new Promise((res) => setTimeout(res, 600));

    weatherData.forEach((value, index) => {
      savedCities.value[index].weather = value.data;
    })
  }
};
await getCities();

const router = useRouter();
const goToCityView = (city) => {
  console.log(city.city)
  localStorage.place_name = city.city;
  router.push({
    name: "cityView",
    params: { state: city.state, city: city.city },
    query: {
      id: city.id,
      lat: city.coords.lat,
      lng: city.coords.lng,
    },
  })
}
</script>