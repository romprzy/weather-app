<template>
  <div id="app" class="text-center">
    <h1 class="text-secondary">{{ title }}</h1>

    <div v-if="message" class="alert alert-danger">
      {{ message }}<br>
      Please try again later...
    </div>
    <DisplayWeather v-if="weather" :weather="this.weather" :offline="offline" />

    <div class="status mt-4 text-left text-secondary">
      <span class="status-icon" :class="{'bg-success': !offline, 'bg-warning': offline}"></span>
      <span v-if="offline">Offline</span>
      <span v-else>Online</span>
    </div>

    <div class="spinner" v-if="loading">
      <div class="spinner-border text-primary" role="status"><span class="sr-only">Loading...</span></div>
    </div>
  </div>
</template>

<script>
import DisplayWeather from './components/DisplayWeather.vue';

export default {
  name: 'app',
  components: {
    DisplayWeather
  },
  data () {
    return {
      title: 'Weather app',
      loading: false,
      offline: true,
      message: false,
      api: {
        base_url: 'http://api.openweathermap.org/data/2.5/weather',
        appid: 'ade7a099018e4beaa468a7b3dbab2c04'
      },
      location: {},
      weather: false
    }
  },
  computed: {
    url () {
      let location = {};
      if (this.location && this.location.lat && this.location.lon) {
        location = `lat=${this.location.lat}&lon=${this.location.lon}`;
      }

      return `${this.api.base_url}?appid=${this.api.appid}&${location}`;
    }
  },
  methods: {
    getLocation () {
      if (navigator && 'geolocation' in navigator) {
        navigator.geolocation.getCurrentPosition(
          position => {
            this.location = {
              lat: position.coords.latitude,
              lon: position.coords.longitude
            }
          },
          error => {
            this.message = error.message;
          }
        );
      } else {
        this.message = 'Geolocation is not available on this device.';
      }
    },
    getWeather () {
      this.loading = true;
      fetch (this.url)
        .then(response => {
          return response.json();
        })
        .then(body => {
          this.weather = body;
          this.saveToLocalStorage('weather', body);
          this.loading = false;
          this.offline = false;
        })
        .catch (error => {
          this.loading = false;
          if (this.getFromLocalStorage('weather')) {
            this.weather = this.getFromLocalStorage('weather');
            this.offline = true;
          }
          if ('message' in error) {
            console.log('getWeather Wrror');
            this.message = error.message;
          }
        })
    },
    getFromLocalStorage (item) {
      const data = JSON.parse(localStorage.getItem(item));
      return data;
    },
    saveToLocalStorage (item, data) {
      localStorage.setItem(item, data);
    }
  },
  created () {
    this.getLocation();
  },
  mounted () {
    window.addEventListener('getWeatherError', () => {
      console.log('getWeatherError event');
      this.weather = this.getFromLocalStorage('weather');
    });
  },
  watch: {
    location () {
      this.getWeather(this.location);
      this.saveToLocalStorage ('location', JSON.stringify(this.location));
    },
    weather () {
      this.saveToLocalStorage ('weather', JSON.stringify(this.weather));
    }
  }
}
</script>

<style lang="scss">
  body {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    height: 100vh;
    font-weight: 300;
  }

  h1 {
    font-weight: 300;
    font-size: 2.5rem;
    margin-bottom: 1em;
  }

  .spinner {
    position: fixed;
    z-index: 9999;
    top: 0;
    left: 0;
    bottom: 0;
    right: 0;
    display: flex;
    align-items: center;
    justify-content: center;
    background: rgba(0, 0, 0, .75);
    transition: background .35s;
  }

  .status-icon {
    display: inline-block;
    border-radius: 50%;
    width: .75rem;
    height: .75rem;
    margin-right: .5em;
    vertical-align: baseline;
  }
</style>
