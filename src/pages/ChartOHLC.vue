<template>
  <div v-if="isLoaded" id="chart">
    <apexchart type="candlestick" height="600" :options="chartOptions" :series="series"></apexchart>
    <ul class="my-list">
      <li class="my-list-item"><span class="dot-green"></span>Upward trending</li>
      <li class="my-list-item"><span class="dot-red"></span>Downward trending</li>
    </ul>
  </div>
  <div v-else>
      <q-skeleton class="bg-grey"/>
  </div>
</template>
<script>
import { api } from 'boot/axios'
export default {
    props: ['trading_code'],
    data: function() {
      return {
        chartOptions: {
            chart: {
              type: 'candlestick',
              height: 350
            },
            title: {
              text: '',
              align: 'left'
            },
            xaxis: {
              type: 'datetime'
            },
            yaxis: {
              tooltip: {
                enabled: true
              }
            }
          },
        series: [{
            data: []
        }],
        isLoaded: false
      }
    },
    mounted(){
    let months = ["Jan", "Feb", "Mar", "Apr", "May", "Jun", "Jul", "Aug", "Sept", "Oct", "Nov", "Dec"];
    api.get(`/companies/${this.trading_code}/data`)
      .then((response) => {
        if(response.status === 200){
          var data = JSON.parse(response.data.data);
          data = data.reverse();
          //console.log(data);
          data.forEach(element => {
            var date = new Date(element.fields.parsed_date);
            // date = date.getUTCDate()+"-"+months[date.getUTCMonth()];

            var o = parseFloat(element.fields.yesterdays_closing_price);
            var h = parseFloat(element.fields.high);
            var l = parseFloat(element.fields.low);
            var c = parseFloat(element.fields.closing_price);
            this.series[0].data.push(
              {
                x: date,
                y: [o, h, l, c]
              }
            );
          });
          this.isLoaded = true;
        }
        else
        {
          console.log("data load failed");
        }
      })
  }
};
</script>