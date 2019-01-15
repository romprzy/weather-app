<template>
  <div class="weather">
    <h2 v-if="weather.name">{{ weather.name }}</h2>
    <div>
      <img v-if="icon" :src="`http://openweathermap.org/img/w/${icon}.png`" :alt="description" >
      <div v-if="description">{{ description }}</div>
      <div v-if="temp" v-html="temp"></div>
      <div v-if="pressure">{{ pressure }}</div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'DisplayWeather',
  props: {
    weather: {
      type: Object
    }
  },
  computed: {
    description () {
      return this.weather.weather[0].description;
    },
    icon () {
      return this.weather.weather[0].icon;
    },
    humidity () {
      return this.weather.main.humidity;
    },
    temp () {
      return Number(this.weather.main.temp) - 272.15 + '&deg;C';
    },
    pressure () {
      return this.weather.main.pressure + ' hPa';
    },
  },
  data () {
    return {
    }
  },
  mounted () {
    console.log(this.weather);
  }
}
</script>

<style lang="scss">
body {
  background: #333;
  color: #fff;
}

.weather {
  border: 1px solid rgba(255, 255, 255, .25);
  display: inline-flex;
  flex-direction: column;
  padding: 20px 40px;
}

h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
