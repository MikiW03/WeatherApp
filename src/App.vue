<template>
  <input v-model="cityInput" @keyup.enter="submitCity">
  <button @click="submitCity">Submit</button>
  {{ data }}
</template>

<script>
import { ref, computed } from 'vue'
import { useFetch } from './composables/fetch.js'

export default {
  setup() {
    function submitCity() {
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

    return {
      data,
      city,
      cityInput,
      URL,
      submitCity
    }
  }
}

</script>

<style>
</style>
