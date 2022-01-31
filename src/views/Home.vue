<template>
  <main class="l-page" :class="typeof weather.main != 'undefined' ? lowerCase(weather.weather[0].main) : '' ">
    <div class="c-search-box">
      <input 
        class="c-search-input"
        type="text"
        placeholder="Search..." 
        v-model="query"
        @keypress="fetchWeather"
      />
    </div>

     {{lat}}
        <p v-if="long"> {{long}}</p>

    <div class="c-weather-wrap" v-if="typeof weather.main != 'undefined'">
      <div class="c-weather__location-box">
        <div class="c-weather__location">{{weather.name}}, {{weather.sys.country}}</div>
        <div class="c-weather__location-date">{{ dateBuilder() }}</div>
      </div>

      <div class="c-weather__box">
        <div class="c-weather__temp">{{ Math.round(weather.main.temp) }}°c</div>
        <div class="c-weather__state">{{ weather.weather[0].main }}</div>
      </div>

      <div class="c-weather__list">
        <div class="c-weater_list-item"></div>
        <div class="c-weater_list-item"></div>
        <div class="c-weater_list-item"></div>
        <div class="c-weater_list-item"></div>
        <div class="c-weater_list-item"></div>
        <div class="c-weater_list-item"></div>
      </div>

    </div>

  </main>
</template>

<script>
import { readonly } from 'vue';
// @ is an alias to /src
//import HelloWorld from '@/components/HelloWorld.vue'

import {ApiKeys} from '@/config/config'

export default {
  name: 'Home',
  data() {
    return {
      api_key: ApiKeys.weatherAPI,
      url_base: 'https://api.openweathermap.org/data/2.5/',
      query: '',
      weather: {},
      weatherInfo: {},
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


     // console.log(position.coords.latitude);
     // console.log(position.coords.longitude);
     // console.log(position.coords);
      //console.log("show positon")
      //return position.coords.latitude;
      //console.log(lat);
    }

    function error(err) {
      //console.warn(`ERROR(${err.code}): ${err.message}`);
      _this.$data.lat = -36.8667;
      _this.$data.long = 174.7667;
    }

  },
  methods: {

    fetchWeatherInfo(){
      fetch(`${this.url_base}/onecall?lat=${this.lat}&lon=${this.long}&exclude=minutely,hourly,alerts&units=metric&APPID=${this.api_key}`)
        .then(res => {
          return res.json();
        })
        .then(this.setInfo)
    },

    setInfo(res) {
      console.log(res);
      this.weatherInfo = res;
    },

    // fetch weather 
    fetchWeather(e) {
      if(e.key == "Enter"){
        fetch(`${this.url_base}weather?q=${this.query}&units=metric&APPID=${this.api_key}`)
        .then(res => {
          return res.json();
        })
        .then(this.setResult)
      }
    },


    // update weather data 
    setResult(result) {
      console.log(result)
      console.log(this.long)
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

    // convert lower cases
    lowerCase(str){
      return `${str.toLowerCase()}`;
    }
  },
  updated(){
    // 
    if(Object.keys(this.weatherInfo).length === 0){
      this.fetchWeatherInfo()
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
    flex: 1;
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
    font-size: 30px;
    font-weight: bold;
    text-shadow: 3px 6px rgba($black, 0.25);

  }

</style>
