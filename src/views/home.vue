<template>
  <div
    class="relative bg-cover h-[90%] w-[90%] rounded-lg text-white flex"
    :style="{ backgroundImage: `url(/images/${mainWeather}.jpg)` }"
  >
    <Sidebar :weatherData="weatherData" @searchLocation="searchLocation" />
    <Dashboard :weatherData="weatherData" />
    <Transition name="fade">
      <Alert v-if="isShowAlert" :alertText="alertText" />
    </Transition>
  </div>
</template>

<script setup lang="ts">
import Sidebar from '@/components/Sidebar.vue';
import Alert from '@/components/Alert.vue';
import Dashboard from '@/components/Dashboard.vue';
import { computed, onMounted, reactive, ref } from 'vue';
import type { WeatherData } from 'types/api';
import axios from 'axios';

let alertText = ref<string>('');

let isShowAlert = ref<boolean>(false);

let weather = ref<string>('');

let weatherData = reactive<WeatherData>({
  cod: '200',
  message: 0,
  cnt: 40,
  list: [
    {
      dt: 0,
      main: {
        temp: 0,
        feels_like: 0,
        temp_min: 0,
        temp_max: 0,
        pressure: 0,
        sea_level: 0,
        grnd_level: 0,
        humidity: 0,
        temp_kf: 0
      },
      weather: [
        {
          id: 0,
          main: '',
          description: '',
          icon: ''
        }
      ],
      clouds: {
        all: 0
      },
      wind: {
        speed: 0,
        deg: 0,
        gust: 0
      },
      visibility: 0,
      pop: 0,
      rain: {
        '3h': 0
      },
      sys: {
        pod: ''
      },
      dt_txt: ''
    }
  ],
  city: {
    id: 0,
    name: '',
    coord: {
      lat: 0,
      lon: 0
    },
    country: '',
    population: 0,
    timezone: 0,
    sunrise: 0,
    sunset: 0
  }
});

const searchLocation = async (city: string = 'tokyo') => {
  await axios
    .get('https://api.openweathermap.org/data/2.5/forecast', {
      params: {
        appid: '3859d9601a1af13c5702a75270411fe3',
        q: city
      }
    })
    .then((response) => {
      let data = response.data;
      let newLists = data.list.slice(0, 7);
      data.list = newLists;
      weatherData = Object.assign(weatherData, data);
      weather.value = data.list[0].weather[0].main;
    })
    .catch((error) => {
      setAlert(error.response.data.message);
      console.error(error);
    });
};

const setAlert = (message: string) => {
  isShowAlert.value = true;
  alertText.value = message;
  setTimeout(() => {
    isShowAlert.value = false;
  }, 5000);
};

const mainWeather = computed(() => {
  switch (weather.value) {
    case 'Thunderstorm':
      return 'thunderstorm';

    case 'Drizzle':
      return 'drizzle';

    case 'Rain':
      return 'rain';

    case 'Snow':
      return 'snow';

    case 'Clear':
      return 'clear';

    case 'Clouds':
      return 'clouds';

    default:
      return 'mist';
  }
});

onMounted(async () => {
  await searchLocation();
});
</script>

<style lang="scss" scoped></style>
