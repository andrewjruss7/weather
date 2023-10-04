<template>
  <div id="app" :class="typeof weather.main != 'undefined' && weather.main.temp >= 15 ? 'warm' : ''">
    <main>
      <div class="search-box">
        <input type="text" class="search-nav" placeholder="Search.." v-model="query" @keypress="fetchWeather">
      </div>
      <div class="weather-wrap" v-if="typeof weather.main != 'undefined'">
        <div class="location-box">
          <div class="location"> {{weather.name}}, {{weather.sys.country}} </div>
          <div class="date">{{ dateBuilder() }}</div>
        </div>

        <div class="weather-box">
          <div class="temp">{{ Math.round(weather.main.temp)}}°C</div>
        <div class="weather">{{ weather.weather[0].main }}</div>
        </div>

        <div class="weather-btn">
          <button @click="getWeatherByLocation" :disabled="isLoading">
            {{ isLoading ? 'Cargando...' : 'My Location'}}
          </button>
        </div>

      </div>
    </main>
  </div>
</template>

<script>

export default {
  name: 'App',
 data() {
    return {
      api_key: '5e3fc4e0d78dc4db4fe2ad97c69a1e8e',
      api_base: 'https://api.openweathermap.org/data/2.5/',
      query: 'Barranquilla',
      weather: {},
      isLoading: false,
    }
 },
  methods: {
    fetchWeather(e, useLocation = false) {
      if (e.key === "Enter" || useLocation) {
        fetch(`${this.api_base}weather?q=${useLocation ? 'Barranquilla' : this.query}&units=metric&APPID=${this.api_key}`)
            .then(res => {
              return res.json();
            }).then(this.setResults);
      }
    },
    getWeatherByLocation() {
      this.isLoading = true;

      if(navigator.geolocation) {
        navigator.geolocation.getCurrentPosition((position) => {
          const latitude = position.coords.latitude;
          const longitude = position.coords.longitude;

          fetch(`${this.api_base}weather?lat=${latitude}&lon=${longitude}&units=metric&APPID=${this.api_key}`)
              .then((res) => res.json())
              .then(this.setResults)
          this.isLoading = false;
        });
      } else {
        console.error('Geolocation is not available in this browser!');
        this.isLoading = false;
      }
    },
    setResults(results) {
      this.weather = results;
      this.query = '';
    },
    dateBuilder() {
      let d = new Date();
      let months = ["January", "February", "March", "April", "May", "June", "July",
      "August", "September", "October", "November", "December"];
      let days = ["Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"];

      let day = days[d.getDay()];
      let date = d.getDate();
      let month = months[d.getMonth()];
      let year = d.getFullYear();

      return `${day} ${date}, ${month} ${year}`;
    },
  },
  mounted() {
    this.fetchWeather({ key: 'Enter' });
  }
}
</script>

<style>
#app {
  background-image: url("./assets/cold-bg.jpg");
  background-size: cover;
  background-position: bottom;
  transition: 0.4s;
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

#app.warm {
  background-image: url('./assets/warm-bg.jpg');
}

main {
  min-height: 100vh;
  padding: 25px;
  background-image: linear-gradient(to bottom, rgba(0,0,0,0.25), rgba(0,0,0,0.75));
}

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

.search-box {
  width: 100%;
  margin-bottom: 30px;
}

.search-box .search-nav {
  display: block;
  width: 100%;
  padding: 15px;

  color:#313131;
  font-size: 20px;

  appearance: none;
  border: none;
  outline: none;

  box-shadow: 0 0 8px rgba(0,0,0,0.25);
  background: rgba(255, 255, 255, 0.5) none;
  border-radius: 25px;
  transition: 0.4s;
}

.search-box .search-nav:focus {
  background: rgba(255, 255, 255, 0.75);
  box-shadow: 0 0 16px rgba(0,0,0,0.25);
}

.location-box .location {
  color: white;
  font-size: 32px;
  font-weight: 500;
  text-shadow: rgba(255, 255, 255, 0.75);
}

.location-box .date {
  color: white;
  font-size: 20px;
  font-weight: 300;
  font-style: italic;
}

.weather-box .temp {
  display: inline-block;
  padding: 10px 25px;
  color: #FFF;
  font-size: 102px;
  font-weight: 900;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.25);
  border-radius: 16px;
  margin: 30px 0;
  box-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}

.weather-box .weather {
  color: #FFF;
  font-size: 48px;
  font-weight: 700;
  font-style: italic;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}

.weather-btn button {
  background-color: rgba(255, 255, 255, 0.25);
  border: 2px solid rgba(255, 255, 255, 0.25);
  border-radius: 25px;
  color: #fff;
  padding: 10px 20px;
  cursor: pointer;
  transition: background-color 0.3s ease, color 0.3s ease;
}

.weather-btn button:hover {
  background-color: #fff;
  color: #000;
}

</style>
