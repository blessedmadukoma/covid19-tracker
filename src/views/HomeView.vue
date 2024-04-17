<template>
  <main v-if="!covidData.loading">Show data</main>

  <main v-else class="flex flex-col align-center justify-center text-center">
    <div class="text-gray-500 text-3xl mt-10 mb-6">Fetching data</div>

    <img src="../assets/hourglass.gif" class="w-24 m-auto" alt="loading..." />
  </main>
</template>

<script lang="ts" setup>
import axios from 'axios'
import { onMounted, reactive } from 'vue'

type CovidDataType = {
  loading: boolean
  title: string
  dataDate: string
  stats: {}
  countries: []
}

const covidData: CovidDataType = reactive({
  loading: true,
  title: 'Global',
  dataDate: '',
  stats: {},
  countries: []
})

const fetchCovidData = async () => {
  const options = {
    method: 'GET',
    url: import.meta.env.VITE_RAPI_API_COVID_URL,
    params: {
      country: 'All'
    },
    headers: {
      'X-RapidAPI-Key': import.meta.env.VITE_RAPID_API_KEY,
      'X-RapidAPI-Host': import.meta.env.VITE_RAPID_API_HOST
    }
  }

  try {
    const response = await axios.request(options)

    let data = response?.data?.response[0]

    covidData.loading = false
    covidData.countries = data.country
    // covidData.dataDate = data.day + data.time
    covidData.dataDate = data.time
    covidData.stats = {
      active: data.cases.active,
      critical: data.cases.critical,
      new: data.cases.new,
      recovered: data.cases.recovered,
      total: data.cases.total,
      deaths: data.deaths.total
    }

    console.log(covidData)

    return covidData
  } catch (error) {
    console.error(error)
  }
}

onMounted(() => {
  fetchCovidData()
})
</script>
