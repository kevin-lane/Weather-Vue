<script setup lang="ts">
import { ref } from 'vue';
import LocalTime from './components/LocalTime.vue';
import Weather from './components/Weather.vue';
import WeeklyForecast from './components/WeeklyForecast.vue';
import SearchBar from './components/SearchBar.vue';

const date = new Date();
const day = date.getDate();
const month = date.getMonth() + 1;
const currentMonth = month < 10 ? '0' + month : month; //If month less than 10, append 0
const currentDay = day < 10 ? '0' + day : day;
const year = date.getFullYear();
const currentDate = `${year}-${currentMonth}-${currentDay}`;


const city = ref('');
const continent = ref('');
const country = ref('');
const locationDate = ref(0);
const locationMonth = ref(0);
const locationYear = ref(0);
const locationDayOfWeek = ref('');
const locationTime = ref('');
const weatherCondition = ref('');
const temperature = ref(0);
const lowestTemp = ref(0);
const highestTemp = ref(0);
const temperaturesArr = ref<any>([]);


const apiKey = import.meta.env.VITE_WEATHER_API_KEY;
const apiUrl = `https://api.openweathermap.org/data/2.5/weather?units=metric`;
const apiForecast = `https://api.openweathermap.org/data/2.5/forecast?units=metric`;
const timeAPI = `https://timeapi.io/api/time/current/zone?`;


function searchCity(query:string){
  city.value = query;
  //If previous forecast in array, remove and replace with new citys forecast
  if (temperaturesArr.value.length > 0) {
      temperaturesArr.value.splice(-5);
  }
  //Only run these functions if a city has value
  //Had to pass in city as a parameter to functions so that
  //Right data will show up and match with right city
  if (city.value !== '') {
    checkWeather(city.value);
    getForecast(city.value);
    fetchTime(city.value);
  }
}

function setDates(date: string, days: number) : string {
  const newDate = new Date(date);
  newDate.setDate(newDate.getDate() + days)
  const day = newDate.getDate();
  const month = newDate.getMonth() + 1;
  const currentMonth = month < 10 ? '0' + month : month; //If month less than 10, append 0
  const currentDay = day < 10 ? '0' + day : day;
  const year = newDate.getFullYear();
  const currentDate = `${year}-${currentMonth}-${currentDay}`;

  return currentDate;
}

async function fetchTime(city:string) {
  const response = await fetch(timeAPI + `timeZone=asia%2F${city}`);
  var data = await response.json();
  locationYear.value = data.year;
  locationMonth.value = data.month;
  locationDate.value = data.day < 10 ? '0' + data.day : data.day;
  locationDayOfWeek.value = data.dayOfWeek;
  locationTime.value = data.time;
}

async function checkWeather(city:string) {
  const response = await fetch(apiUrl + `&q=${city}&appid=${apiKey}`);
  var data = await response.json();
  city = data.name;
  country.value = data.sys.country;
  temperature.value = data.main.temp;
  lowestTemp.value = data.main.temp_min;
  highestTemp.value = data.main.temp_max;
  weatherCondition.value = data.weather[0].description;
}

async function getForecast(city:string) {
  const response = await fetch(apiForecast + `&q=${city}&appid=${apiKey}`);
  var data = await response.json();
  console.log(data.list);

  const nextFiveDaysForecast = data.list.filter((temp:any) => {
      return temp.dt_txt.substring(0, 10) !== currentDate
    }
  );

  for (let i = 1; i < 6; i++) {
    const date = setDates(currentDate, i);

    //Lowest temperature for the day
    const lowestTemp = nextFiveDaysForecast.filter((item:any) => item.dt_txt.startsWith(date)).reduce((minItem: any, currentItem:any) => {
      return currentItem.main.temp < minItem.main.temp ? currentItem : minItem;
    }, nextFiveDaysForecast[0]);

    //Highest temperature for the day
    const highestTemp = nextFiveDaysForecast.filter((item:any) => item.dt_txt.startsWith(date)).reduce((maxItem: any, currentItem:any) => {
      return currentItem.main.temp > maxItem.main.temp ? currentItem : maxItem;
    }, nextFiveDaysForecast.filter((item: any) => item.dt_txt.startsWith(date))[0]);

    temperaturesArr.value.push({
        date: date,
        lowestTemperature: lowestTemp.main.temp,
        highestTemperature: highestTemp.main.temp
    });
  }
}
</script>

<!-- <script lang="ts"> -->
  <!-- export default {
    methods: {
      searchCity(query:string){
        console.log("Search button clicked");
        console.log(query);
      }
    }
  } -->
<!-- </script> -->

<template>
  <SearchBar v-model:searchQuery="city" @search-city="() => searchCity(city)"/>

  <p class="text-center text-xl" v-if="city === ''">Please enter a city</p>
  <template v-else>

      <p class="text-center text-xl">The weather forecast in {{ city }}, {{ country }}</p>
      <div class="min-[1001px]:flex justify-evenly w-full mt-6  max-[1000px]:grid space-y-6 w-1/2 mb-4 place-content-center">
        <!-- <LocalTime :time="locationTime" :year="locationYear" :month="locationMonth" :day="locationDate" :day-of-week="locationDayOfWeek"/> -->
        <Weather
          :temperature="temperature"
          :lowest-temperature="lowestTemp"
          :highest-temperature="highestTemp"
          :weather-conditions="weatherCondition"
        />
        <WeeklyForecast :temps="temperaturesArr" />
    </div>
  </template>
</template>
