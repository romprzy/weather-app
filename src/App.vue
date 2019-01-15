<template>
  <div id="app">
    <h1>{{ title }}</h1>
    <div v-if="message">{{ message }}</div>
    <DisplayWeather v-if="weather" :weather="this.weather" />
  </div>
</template>

<script>
import DisplayWeather from './components/DisplayWeather.vue'

export default {
  name: 'app',
  components: {
    DisplayWeather
  },
  data () {
    return {
      title: 'Weather app',
      api: {
        base_url: 'http://api.openweathermap.org/data/2.5/weather',
        appid: 'ade7a099018e4beaa468a7b3dbab2c04'
      },
      lat: false,
      lon: false,
      city: false,
      country_code: false,
      q: false,
      message: false,
      body: false,
      weather: false
    }
  },
  computed: {
    url () {
      let location = '';
      if (this.lat && this.lon) {
        location = `lat=${this.lat}&lon=${this.lon}`;
      } else if (this.city) {
        location = `q=${this.city}`
        if (this.country_code) {
          location += `,${this.country_code}`
        }
      }

      return `${this.api.base_url}?appid=${this.api.appid}&${location}`;
    }
  },
  methods: {
    getLocation () {
      if (navigator && 'geolocation' in navigator) {
        navigator.geolocation.getCurrentPosition(
          position => {
            console.log('position', position);
            this.lat = position.coords.latitude;
            this.lon = position.coords.longitude;

            this.getWeather({ lat: position.coords.latitude, lon: position.coords.longitude });
          },
          error => {
            this.message = error.message;
          }
        );
      } else {
        this.message = 'Geolocation is not available. Please type in latitude and longitude';
      }
    },
    getWeather ({ lat, lon }) {
      fetch (this.url)
        .then(response => {
          console.log('weather', response);
          return response.json();
        })
        .then(body => {
          console.log('body', body);
          this.weather = body;
        })
        .catch (error => {
          console.log('error', error);
          if ('message' in error) {
            this.message = error.message;
          }
        })
    }
  },
  created () {
    this.getLocation();
  }
}
</script>

<style lang="scss">
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  margin-top: 60px;
}
</style>
