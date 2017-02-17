<template>
  <div class="tile is-ancestor">
    <div class="tile is-parent">
      <article class="tile is-child box">
        <p class="title control" :class="{'is-loading': isloading}">
          Axios Demo
        </p>
        <a href="http://dev.markitondemand.com" class="link">Markit On Demand - Market Data APIs</a>
        <chart :type="'line'" :data="stockData" :options="options"></chart>
      </article>
    </div>
  </div>
</template>

<script>
import Chart from 'vue-bulma-chartjs'

const api = '/MODApis/Api/v2/InteractiveChart/json'
const params = {
  'Normalized': false,
  'NumberOfDays': 450,
  'DataPeriod': 'Month',
  'Elements': [{'Symbol': 'AAPL', 'Type': 'price', 'Params': ['c']}]
}

export default {
  components: {
    Chart
  },

  data () {
    return {
      data: [],
      labels: [],
      isloading: true,
      options: {
        title: {
          display: true,
          text: 'Price History of AAPL',
          fontSize: 16,
          fontColor: '#1fc8db'
        },
        legend: {
          display: false
        },
        animation: {
          duration: 0
        },
        scales: {
          xAxes: [{
            type: 'time',
            time: {
              unit: 'month'
            }
          }]
        }
      }
    }
  },

  computed: {
    stockData () {
      return {
        labels: this.labels,
        datasets: [{
          fill: false,
          lineTension: 0.25,
          data: this.data,
          label: 'Close price',
          backgroundColor: '#1fc8db',
          pointBackgroundColor: '#1fc8db',
          pointBorderWidth: 1,
          pointHoverRadius: 5,
          pointHoverBackgroundColor: '#1fc8db',
          pointHoverBorderWidth: 1,
          pointRadius: 2
        }]
      }
    }
  },

  mounted () {
    this.$http({
      url: api,
      transformResponse: [(data) => {
        return JSON.parse(data.replace(/T00:00:00/g, ''))
      }],
      params: {
        parameters: params
      }
    }).then((response) => {
      let dates = response.data.Dates
      let price = response.data.Elements[0].DataSeries.close.values
      this.data.push(...price)
      this.labels.push(...dates)
      this.isloading = false
    }).catch((error) => {
      console.log(error)
    })
  }
}
</script>

<style scoped>
</style>
