<template>
  <div class="input-wrapper">
    <input v-model="cityInput" @keyup.enter="submitCity">
    <button @click="submitCity">Search</button>
  </div>
  <div v-if="!data?.hasOwnProperty('error') && data" class="content">
    <div class="actual-data">
      <div class="location">{{ data?.location.name }}, {{ data?.location.country }}</div>
      <div class="last-updated">last update: {{ data?.current.last_updated }}</div>
      <div class="local-time">local time: {{ data?.location.localtime }}</div>
      <div class="temperature">
        <div v-if="celsiusUnit">{{ data?.current.temp_c }}°C</div>
        <div v-else>{{ data?.current.temp_f }}°F</div>
        <button class="unitBtn" @click="celsiusUnit = !celsiusUnit">
          <span v-if="!celsiusUnit">°C</span>
          <span v-else>°F</span>
        </button>
      </div>
      <div class="astro">
        <p>
          <span>Sunrise</span>
          <span>{{ data?.forecast.forecastday[0].astro.sunrise }}</span>
          <img src="https://img.icons8.com/office/344/sunrise--v1.png" alt="">
        </p>
        <p>
          <span>Sunset</span>
          <span>{{ data?.forecast.forecastday[0].astro.sunset }}</span>
          <img src="https://img.icons8.com/office/344/sunset--v1.png" alt="">
        </p>
      </div>
    </div>
    <table class="forecast">
      <tr v-for="(fDay, index) in data.forecast.forecastday" :key="index" class="forecast-day">
        <td class="day-name">
          {{ index == 0 ? "Today" : dayNumberToName(new Date(fDay.date).getDay()) }}
        </td>
        <td class="day-img">
          <img :src="fDay.day.condition.icon" :alt="fDay.day.condition.text">
        </td>
        <template v-if="celsiusUnit">
          <td class="day-max">{{ Math.round(fDay.day.maxtemp_c) }}°C</td>
          <td class="day-min">{{ Math.round(fDay.day.mintemp_c) }}°C</td>
        </template>
        <template v-else>
          <td class="day-max">{{ Math.round(fDay.day.maxtemp_f) }}°F</td>
          <td class="day-min">{{ Math.round(fDay.day.mintemp_f) }}°F</td>
        </template>
      </tr>
    </table>
  </div>
  <div v-else>
    {{ data?.error.message }}
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

    function dayNumberToName(day) {
      switch (day) {
        case 0:
          return 'Sunday';
        case 1:
          return 'Monday';
        case 2:
          return 'Tuesday';
        case 3:
          return 'Wednesday';
        case 4:
          return 'Thursday';
        case 5:
          return 'Friday';
        case 6:
          return 'Saturday';
      }
    }

    const city = ref("Warszawa")
    const cityInput = ref("")
    const days = ref(3)

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
      dayNumberToName,
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
  font-family: Helvetica;
}

:root {
  --bg-color: rgb(89, 153, 225);
  --primary-color: rgba(255, 255, 255, 0.5);
}

body {
  font-size: 20px;
  font-weight: bold;
  background-color: black;
}

#app {
  background-color: var(--bg-color);
  box-shadow: 0 0 1em var(--bg-color);
  padding: 1em;
  margin: auto;
  max-width: 500px;
  min-height: 100vh;
  max-height: 100%;
}

.input-wrapper {
  display: flex;
  width: 100%;
  justify-content: space-between;
  margin-block: 0 50px;
}

.input-wrapper input {
  padding: 0.2em;
  width: 75%;
  font-size: 18px;
}

.input-wrapper button {
  background-color: var(--primary-color);
  width: 20%;
  font-size: 18px
}

.location {
  margin-block: 0 5px;
  text-align: center;
  font-size: 24px;
  margin-inline: auto;
  width: 80%;
  background-color: var(--primary-color);
}

.last-updated,
.local-time {
  text-align: center;
  font-size: 16px;
  margin-block: 0 5px;
  margin-inline: auto;
  width: 75%;
  background-color: var(--primary-color);
}

.temperature {
  width: 150px;
  height: 150px;
  margin-inline: auto;
  margin-block: 50px;
  font-size: 40px;
  background-color: var(--primary-color);
  border-radius: 10px;
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

.astro {
  background-color: var(--primary-color);
  margin: 1em;
  padding: 0.5em;
  display: flex;
  justify-content: space-around;
  text-align: center;
}

.astro>p {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.astro>p>img {
  max-width: 30%;
}

.forecast {
  padding: 1em;
  width: 100%;
  border-spacing: 0 0.5em;
}

.forecast-day {
  width: 100%;
  border-radius: 10px;
  background-color: var(--primary-color);
}

.forecast-day>* {
  text-align: left;
  padding-inline: 0.5em 0;
}
</style>
