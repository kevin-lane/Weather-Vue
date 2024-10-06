<script setup lang="ts">
  import LocalTime from './components/LocalTime.vue';
  import Weather from './components/Weather.vue';
  import WeeklyForecast from './components/WeeklyForecast.vue';
  import SearchBar from './components/SearchBar.vue';
  const timeAPI = `https://timeapi.io/api/time/current/zone?`;
</script>

<script lang="ts">
  export default {
    data(){
      return {
        date: new Date(),
        day: 0,
        month: 1,
        year: 0,
        currentDate: '',
        apiKey: import.meta.env.VITE_WEATHER_API_KEY,
        apiUrl: 'https://api.openweathermap.org/data/2.5/weather?units=metric',
        apiForecast: 'https://api.openweathermap.org/data/2.5/forecast?units=metric',
        city: '',
        country: '',
        locationDate: 0,
        locationMonth: 0,
        locationYear: 0,
        locationDayOfWeek: '',
        locationTime: '',
        weatherCondition: '',
        temperature: 0,
        lowestTemp: 0,
        highestTemp: 0,
        temperaturesArr: [] as any,
        searched: false
      }
    },
    methods: {
      searchCity(query:string){
        this.searched = true;
        this.city = query;
        //If previous forecast in array, remove and replace with new citys forecast
        if (this.temperaturesArr.length > 0) {
          this.temperaturesArr.splice(-5);
        }
        //Only run these functions if a city has value
        //Had to pass in city as a parameter to functions so that
        //Right data will show up and match with right city
        if (this.city !== '') {
          this.checkWeather(this.city);
          this.getForecast(this.city);
          // this.fetchTime(this.city);
        }
      },
      setDates(date: string, days: number) : string {
        const newDate = new Date(date);
        newDate.setDate(newDate.getDate() + days)
        const day = newDate.getDate();
        const month = newDate.getMonth() + 1;
        const currentMonth = month < 10 ? '0' + month : month; //If month less than 10, append 0
        const currentDay = day < 10 ? '0' + day : day;
        const year = newDate.getFullYear();
        const currentDate = `${year}-${currentMonth}-${currentDay}`;

        return currentDate;
      },
      // async fetchTime(city:string) {
      //   const response = await fetch(timeAPI + `timeZone=asia%2F${city}`);
      //   var data = await response.json();
      //   this.locationYear = data.year;
      //   this.locationMonth = data.month;
      //   this.locationDate = data.day < 10 ? '0' + data.day : data.day;
      //   this.locationDayOfWeek = data.dayOfWeek;
      //   this.locationTime = data.time;
      // },
      async checkWeather(city:string){
        const response = await fetch(this.apiUrl + `&q=${city}&appid=${this.apiKey}`);
        var data = await response.json();
        city = data.name;
        this.country = data.sys.country;
        this.temperature = data.main.temp;
        this.lowestTemp = data.main.temp_min;
        this.highestTemp = data.main.temp_max;
        this.weatherCondition = data.weather[0].description;
      },
      async getForecast(city:string) {
        const response = await fetch(this.apiForecast + `&q=${city}&appid=${this.apiKey}`);
        var data = await response.json();
        console.log(data.list);
        this.day = this.date.getDate();
        this.month = this.date.getMonth() + 1;
        this.year = this.date.getFullYear();
        this.currentDate = `${this.year}-${this.month}-${this.day}`;

        const nextFiveDaysForecast = data.list.filter((temp:any) => {
          return temp.dt_txt.substring(0, 10) !== this.currentDate
        });

        for (let i = 1; i < 6; i++) {
          const date = this.setDates(this.currentDate, i);
          //Lowest temperature for the day
          const lowestTemp = nextFiveDaysForecast.filter((item:any) => item.dt_txt.startsWith(date)).reduce((minItem: any, currentItem:any) => {
            return currentItem.main.temp < minItem.main.temp ? currentItem : minItem;
          }, nextFiveDaysForecast[0]);
          //Highest temperature for the day
          const highestTemp = nextFiveDaysForecast.filter((item:any) => item.dt_txt.startsWith(date)).reduce((maxItem: any, currentItem:any) => {
            return currentItem.main.temp > maxItem.main.temp ? currentItem : maxItem;
          }, nextFiveDaysForecast.filter((item: any) => item.dt_txt.startsWith(date))[0]);

          this.temperaturesArr.push({
            date: date,
            lowestTemperature: lowestTemp.main.temp,
            highestTemperature: highestTemp.main.temp
          });
        }
      },
      onInputChange(){
        if(this.city !== ''){
          this.searched = false;
        }
      }
    },
  }
</script>

<template>
  <SearchBar v-model:searchQuery="city" @search-city="() => searchCity(city)" @input-change="onInputChange"/>
  <p class="text-center text-xl" v-if="searched === false">Please enter a city</p>
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
