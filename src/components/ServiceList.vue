<template>
  <div class="shadow-xl rounded-lg neutral-content overflow-x-auto">
    <table class="table service">
      <thead>
      <tr>
        <th>Status</th>
        <th>Service</th>
        <th>2H</th>
        <th>24H</th>
        <th>7D</th>
      </tr>
      </thead>
      <tbody>
      <tr v-for="item in items" :key="item.id" class="h-20">
        <th class="content-center">
          <x-circle-icon v-if="item.status.hasErrors || item.status.hasFailures" class="h-8 w-8 text-red-500"/>
          <exclamation-circle-icon v-else-if="item.status.isDegraded" class="h-8 w-8 text-yellow-400"/>
          <check-circle-icon v-else class="h-8 w-8 text-green-500"/>
        </th>
        <td>
          <div class="text-lg font-medium font-mono">{{ item.name }}</div>
        </td>
        <td>
          <div class="grid grid-rows-1 grid-flow-col items-end">
            <div v-for="data in item.chartData" :key="data.id" class="w-1 bg-green-500 mr-0.5 tooltip" v-bind:class="{'bg-yellow-400': data.isDegraded, 'bg-red-500': data.hasErrors || data.hasFailures}" v-bind:style="{height: (0.5 + 1.5 * data.v) + 'rem'}" v-bind:data-tip="data.responseTime + 'ms'"></div>
          </div>
        </td>
        <td class="font-mono">{{ item['status'].metrics['1dSuccessRatio'] }}%</td>
        <td class="font-mono">{{ item['status'].metrics['7dSuccessRatio'] }}%</td>
      </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import {CheckCircleIcon, XCircleIcon, ExclamationCircleIcon} from '@heroicons/vue/solid'

export default {
  name: "ServiceList",
  components: {CheckCircleIcon, XCircleIcon, ExclamationCircleIcon},
  props: {
    items: Array
  }
}
</script>

<style>
[data-theme="cupcake"] .service :where(tbody th, tbody td) {
  background-color: white;
}

[data-theme="dracula"] .service :where(tbody th, tbody td) {
  background-color: #383D4D;
}
</style>
