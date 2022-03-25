<template>
  <div class="container max-w-3xl md:w-screen px-4">
    <navigation-bar class="my-8"/>
    <p class="mx-8">
      This page provides status information on the services that are part of <a href="https://imink.app"
                                                                                class="link link-primary">imink.app</a>.
      If the listed status does not solve the problem you are experiencing, please contact <a
        class="link link-primary" href="mailto:agent@imink.app">Agent</a>.
    </p>
    <div v-if="results.length == 0" class="my-16">
      <svg class="animate-spin -ml-1 mr-3 h-8 w-full text-primary-focus" xmlns="http://www.w3.org/2000/svg" fill="none"
           viewBox="0 0 24 24">
        <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
        <path class="opacity-75" fill="currentColor"
              d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
      </svg>
    </div>
    <service-list v-else v-bind:items="results" class="my-8"/>
    <global-footer/>
  </div>
</template>

<script>
import ServiceList from './components/ServiceList.vue'
import axios from 'axios'
import {themeChange} from 'theme-change'
import NavigationBar from "@/components/NavigationBar";
import GlobalFooter from "@/components/GlobalFooter";

const getServiceData = async () => {
  try {
    const checklyhqDashboardId = '29bde466'

    const dashboardResponse = await axios.get('https://api.checklyhq.com/dashboards/' + checklyhqDashboardId + '?type=customUrl')
    const accountId = dashboardResponse.data.accountId
    const sign = dashboardResponse.headers['x-signed-dashboard']

    const statusesResponse = await axios.get('https://api.checklyhq.com/dashboards/' + checklyhqDashboardId + '/statuses?type=customDomain&accountId=' + accountId, {
      headers: {'x-signed-dashboard': sign}
    })
    const results = statusesResponse.data.results

    for (const i in results) {
      const result = results[i]
      const respTimeInfoResponse = await axios.get('https://api.checklyhq.com/dashboards/fcd08320/results/' + result.id, {
        headers: {'x-signed-dashboard': sign}
      })
      let data = respTimeInfoResponse.data

      const dataCopy = [...data]
      dataCopy.sort(function (a, b) {
        return a.responseTime - b.responseTime
      })

      let min = 0
      let max = 0
      if (dataCopy.length > 0) {
        min = dataCopy[0].responseTime
        max = dataCopy[dataCopy.length - 1].responseTime
      }

      const v = max - min
      data = data.map(function (item) {
        item.v = (item.responseTime - min) / v
        return item
      })

      result.chartData = data.reverse()
    }
    return results
  } catch (err) {
    console.log(err)
  }
}

export default {
  name: 'App',
  components: {
    NavigationBar,
    ServiceList,
    GlobalFooter
  },
  data: function () {
    return {
      results: []
    };
  },
  async created() {
    this.results = await getServiceData()
    console.log(this.results)
  },
  mounted() {
    themeChange(false)
  }
}
</script>

<style>
</style>
