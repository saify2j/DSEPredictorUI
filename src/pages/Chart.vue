<template>
  <div v-if="isLoaded" class="example">
    <apexchart height="600" type="line" :options="chartOptions" :series="series"></apexchart>
  </div>
</template>

<script>
import { api } from 'boot/axios'
export default {
  name: 'AreaExample',
  props: ['trading_code'],
  data: function() {
    return {
      chartOptions: {
        dataLabels: {
            enabled: false
        },
        stroke: {
            curve: 'smooth'
        },
        xaxis: {
            categories: [],
        },
        tooltip: {
            fixed: {
                enabled: false,
                position: 'topRight'
            }
        }
      }
      ,
      series: [
        {
          name: 'Actual Data',
          data: []
        },
        {
          name: 'Predicted Data',
          data: []
        }
      ],
      isLoaded: false
    }
  },
  methods:{
        calcultaeOHLC(company){
        var o = parseFloat(company.yesterdays_closing_price);
        var h = parseFloat(company.high);
        var l = parseFloat(company.low);
        var c = parseFloat(company.closing_price);
        var ohlc = (o+h+l+c)/4;
        ohlc = Math.round(ohlc*100)/100;
        return ohlc;
    },
  },
  mounted(){
    let months = ["Jan", "Feb", "Mar", "Apr", "May", "Jun", "Jul", "Aug", "Sept", "Oct", "Nov", "Dec"];
    api.get(`/companies/${this.trading_code}/data`)
      .then((response) => {
        if(response.status === 200){
          var data = JSON.parse(response.data.data);
          data = data.reverse();
          //console.log(data);
          var i = 0;
          var pred = 0;
          data.forEach(element => {
            var date = new Date(element.fields.parsed_date);
            //console.log(date);
            if(i != 0)
            {
              this.chartOptions.xaxis.categories.push(date.getUTCDate()+"-"+months[date.getUTCMonth()]);
              var ohlc = this.calcultaeOHLC(element.fields);
              this.series[0].data.push(ohlc);
              this.series[1].data.push(pred);
              pred = parseFloat(element.fields.predicted_next_day_ohlc_price);
            }
            else
            {
              pred = parseFloat(element.fields.predicted_next_day_ohlc_price);
            }
            i++;

          });
          this.isLoaded = true;
        }
        else
        {
          console.log("data load failed");
        }
      })
  }
}
</script>