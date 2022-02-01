<template>
  <main class="l-page" :class="typeof weather.main != 'undefined' ? lowerCase(weather.weather[0].main) : '' ">
    <div class="c-search-box">
      <input 
        class="c-search-input"
        type="text"
        placeholder="Location Search..." 
        v-model="query"
        @keypress="fetchWeather"
      />
    </div>
  
    <div class="c-weather-wrap" v-if="typeof weather.main != 'undefined'">
      <div class="c-weather__location-box">
        <div class="c-weather__location">{{weather.name}}, {{weather.sys.country}}</div>
        <div class="c-weather__location-date">{{ dateBuilder() }}</div>
      </div>

      <div class="c-weather__box">
        <div class="c-weather__temp">{{ Math.round(weather.main.temp) }}°c</div>
        <div class="c-weather__state-icon">
          
        </div>
        <div class="c-weather__discription">{{ weather.weather[0].description }}</div>
        <div class="c-weather__state">L: {{ Math.round(weather.main.temp_min) }}°c H: {{ Math.round(weather.main.temp_max) }}°c</div>
      </div>

      <div class="c-weather__list-wrap" v-if="typeof forecast.daily != 'undefined'">
        <div v-for="(daily, index) in forecast.daily" :key="index" class="c-weater_list-item">
          <p class="c-weather__list-date" v-if="index === 0">Today</p>
          <p class="c-weather__list-date" v-else>{{ getDate(daily.dt) }}</p>
          <div class="c-weather__list-icon-wrapper" ><i class="c-weather__list-icon" :class="'iconf-'+ daily.weather[0].icon"></i></div>
          <p class="c-weather__list-discription">{{daily.weather[0].description}}</p>
          <p class="c-weather__list-state">L:{{ Math.round(daily.temp.min) }}°c  H: {{ Math.round(daily.temp.max) }}°c</p>
        </div>
      </div>

    </div>

  </main>
</template>

<script>
import { readonly } from 'vue';
import {ApiKeys} from '@/config/config'

export default {
  name: 'Home',
  data() {
    return {
      api_key: ApiKeys.weatherAPI,
      url_base: 'https://api.openweathermap.org/data/2.5/',
      query: '',
      weather: {},
      forecast: {},
      location: "",
      lat: null,
      long: null
    }
  },

  created(){
    let options = {
      enableHighAccuracy: true,
      timeout: 5000,
      maximumAge: 0
    };
    
    const _this = this;
    
    navigator.geolocation.getCurrentPosition(showPosition, error, options);
    
    // get current position
    function showPosition(position){
      
      _this.$data.lat = position.coords.latitude;
      _this.$data.long = position.coords.longitude;
    }

    function error(err) {
      //console.warn(`ERROR(${err.code}): ${err.message}`);
      _this.$data.lat = -36.8667;
      _this.$data.long = 174.7667;
    }

  },
  methods: {

    fetchForecast(){
      fetch(`${this.url_base}/onecall?lat=${this.lat}&lon=${this.long}&exclude=minutely,hourly,alerts&units=metric&APPID=${this.api_key}`)
        .then(res => {
          return res.json();
        })
        .then(this.setInfo)
    },

    setInfo(res) {
      console.log(res);
      this.forecast = res;
    },

    // fetch weather 
    fetchWeather(e) {
      if(e.key == "Enter"){
        fetch(`${this.url_base}weather?q=${this.query}&units=metric&APPID=${this.api_key}`)
        .then(res => {
          return res.json();
        })
        .then(this.setResult)
        .then(this.fetchForecast)
      }
    },

    // update weather data 
    setResult(result) {
      console.log(result);
      this.weather = result;
      this.lat = result.coord.lat;
      this.long = result.coord.lon;
    },

    // get current date time
    dateBuilder() {
      let d = new Date();
      let date = d.getDate();

      let month = d.getMonth()+1; 
      let year = d.getFullYear();

      let days = ["Sunday","Monday","Tuesday","Wednesday","Thursday","Friday","Saturday"];
      let day = days[d.getDay()];

      if(date < 10) {
          date = '0' + date;
      } 

      if(month < 10 ) {
          month = '0'+ month;
      }

      return `${day} ${date} / ${month} / ${year} `;
    },

    // convert timestamps to human date time
    getDate(timestamps){
      const date = new Date(timestamps * 1000);
      const options = { weekday: 'short' };
      return date.toLocaleDateString('en-US',options);
    },

    // convert lower cases
    lowerCase(str){
      return `${str.toLowerCase()}`;
    }
  },
  beforeUpdate(){
    // 
    if(Object.keys(this.forecast).length === 0){
      //this.fetchForecast()
    }
  }
}
</script>

<style scoped lang="scss">

  $black: #000;
  $white: #fff;

  .l-page {
    display: flex;
    flex-direction: column;
  }

  .c-search-box {
    width: 100%;
    margin-bottom: 35px;
  }

  .c-search-input {
    display: block;
    width: 100%;
    padding: 15px;
    font-size: 20px;
    color: #666;
    appearance: none;
    border: none;
    background: none;
    background-color: rgba($white, 0.7);
    transition: 0.4s;
    box-shadow: 0 0 8px rgba($black, 0.25);

    &:focus {
      box-shadow: 0 0 16px rgba($black, 0.25);
    }
  }

  .c-weather-wrap {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: stretch;
    margin-top: 30px;
  }

  .c-weather__location-box {
    margin-bottom: 20px;
  }

  .c-weather__location {
    color: $white;
    font-size: 32px;
    font-weight: 500;
    text-align: center;
    text-shadow: 1px 3px rgba($black, 0.25);
  }

  .c-weather__location-date {
    color: $white;
    font-size: 20px;
    font-weight: 100;
    text-align: center;
  }

  .c-weather__box {
    text-align: center;
  }

  .c-weather__temp {
    display: inline-block;
    padding: 10px 25px;
    color: $white;
    font-size: 60px;
    font-weight: bold;
    text-shadow: 3px 6px rgba($black, 0.25);
    background-color: rgba($white, 0.25);
    border-radius: 15px;
    margin: 30px 0;
    box-shadow: 3px 6px rgba($black, 0.25);
  }

  .c-weather__state {
    color: $white;
    font-size: 25px;
    font-weight: bold;
  }

  .c-weather__discription {
    color: $white;
    font-size: 25px;
    font-weight: bold;
  }

  .c-weather__list-wrap {
    display: grid;
    grid-gap: 10px;
    grid-template-columns: repeat(1, 1fr);
    margin-top: 50px;
    @media (min-width:1024px){
      grid-template-columns: repeat(8, 1fr);
      margin-top: 80px;
    }
  }

  .c-weater_list-item{
    display: grid;
    justify-content: space-between;
    grid-template-columns: repeat(3, 1fr);
    align-items: center;
    @media (min-width:680px){
      grid-template-columns: repeat(5, 1fr);
    }
    
    @media (min-width:1024px){
      grid-template-columns: repeat(1, 1fr);
      align-items: flex-start;
    }
  }

  .c-weather__list-date {
    color: $white;
    font-size: 18px;
    font-weight: bold;
    margin-bottom: 5px;

    @media (min-width:680px){
      grid-column:  auto;
    }

    @media (min-width:1024px){
      margin-bottom: 15px;
    }
  }
  
  .c-weather__list-icon-wrapper{
    @media (min-width:1024px){
      margin-bottom: 15px;
    }
  }

  .c-weather__list-state-icon {
    width: 30px;
    @media (min-width:1024px){
      width: 50px;
    }
  }

  .c-weather__list-icon {
    font-size: 40px;
    color: $white;
  }

  .c-weather__list-discription {
    display: none;
    @media (min-width:680px){
      display: block;
      grid-column:  span 2;
    }
    @media (min-width:1024px){
      grid-column:  auto;
    }
  }

  .c-weather__list-discription,
  .c-weather__list-state {
    color: $white;
    font-size: 16px;
    margin-bottom: 5px;
  }

</style>
