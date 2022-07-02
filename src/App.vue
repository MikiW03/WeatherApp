<template>
  <div class="input-wrapper">
    <input v-model="cityInput" @keyup.enter="submitCity">
    <button @click="submitCity">Submit</button>
  </div>
  <div class="content" v-if="!data?.hasOwnProperty('error')">
    <div class="actual-data">
      <div class="location">{{ data?.location.name }}, {{ data?.location.country }}</div>
      <div class="last-updated">last update: {{ data?.current.last_updated }}</div>
      <div class="temperature-wrapper">
        <div class="temperature temperature-celsius" v-if="celsiusUnit">{{ data?.current.temp_c }}°C</div>
        <div class="temperature temperature-fahrenheit" v-else>{{ data?.current.temp_f }}°F</div>
        <button class="unitBtn" @click="celsiusUnit = !celsiusUnit">
          <span v-if="!celsiusUnit">°C</span>
          <span v-else>°F</span>
        </button>
      </div>
    </div>
    <div class=" forecast">
      <div class="forecast-day day-1">
        <div class="day-name">Today</div>
        <div class="day-img"><img src="//cdn.weatherapi.com/weather/64x64/night/113.png" alt=""></div>
        <div class="day-max">20°C</div>
        <div class="day-min">20°C</div>
      </div>
      <div class="forecast-day day-2">
        <div class="day-name">Today</div>
        <div class="day-img"><img src="//cdn.weatherapi.com/weather/64x64/night/113.png" alt=""></div>
        <div class="day-max">20°C</div>
        <div class="day-min">20°C</div>
      </div>
      <div class="forecast-day day-3">
        <div class="day-name">Today</div>
        <div class="day-img"><img src="//cdn.weatherapi.com/weather/64x64/night/113.png" alt=""></div>
        <div class="day-max">20°C</div>
        <div class="day-min">20°C</div>
      </div>
      <div class="forecast-day day-4">
        <div class="day-name">Today</div>
        <div class="day-img"><img src="//cdn.weatherapi.com/weather/64x64/night/113.png" alt=""></div>
        <div class="day-max">20°C</div>
        <div class="day-min">20°C</div>
      </div>
      <div class="forecast-day day-5">
        <div class="day-name">Today</div>
        <div class="day-img"><img src="//cdn.weatherapi.com/weather/64x64/night/113.png" alt=""></div>
        <div class="day-max">20°C</div>
        <div class="day-min">20°C</div>
      </div>
    </div>
  </div>
  <div v-else>
    {{ data.error.message }}
  </div>
</template>

<script>
import { ref, computed } from 'vue'
import { useFetch } from './composables/fetch.js'

export default {
  setup() {
    function submitCity() {
      if (!cityInput.value) return
      city.value = cityInput.value
        .toLowerCase()
        .replace("ł", "l")
        .normalize('NFKD').replace(/[^\w\s.-_/]/g, '')
    }

    const city = ref("Ostrów Wielkopolski")
    const cityInput = ref("")
    const days = ref(5)

    const URL = computed(() => {
      return `http://api.weatherapi.com/v1/forecast.json?key=3ae48e23d975455e872185344220107&q=${city.value}&days=${days.value}&aqi=no&alerts=no`
    })

    const { data } = useFetch(URL)

    const celsiusUnit = ref(true)

    return {
      data,
      city,
      cityInput,
      URL,
      celsiusUnit,
      submitCity,
    }
  }
}

</script>

<style>
*,
*::before,
*::after {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: Roboto;
}

body {
  font-size: 18px;
}

#app {
  padding: 1em;
  margin: auto;
  max-width: 500px;
}

.input-wrapper {
  display: flex;
  width: 100%;
  justify-content: space-between;
  margin-block: 0 50px;
}

.input-wrapper input {
  width: 75%;
  font-size: 18px;
}

.input-wrapper button {
  width: 20%
}

.location {
  margin-block: 0 5px;
  text-align: center;
  font-size: 20px;
}

.last-updated {
  text-align: center;
  margin-block: 0 20px;
  font-size: 16px;
}

.temperature-wrapper {
  width: 120px;
  height: 120px;
  margin: auto;
  font-size: 35px;
  border: 1px solid red;
  display: flex;
  align-items: center;
  justify-content: center;
  position: relative;
}

.unitBtn {
  display: block;
  width: 90%;
  margin-inline: auto;
  margin-top: 10px;
  padding: 0.1em;
  position: absolute;
  bottom: 5px;
  font-size: 15px;
}

.forecast {
  margin-block: 100px 0;
  padding: 1em;
}

.forecast-day {
  display: flex;
  align-items: center;
  justify-content: space-between;
}
</style>
