<template>
  <div class="example">
    <apexchart height="600" type="line" :options="chartOptions" :series="series"></apexchart>
  </div>
</template>

<script>
export default {
  name: 'AreaExample',
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
            categories: ["Jan", "Feb", "Mar", "Apr", "May", "Jun", "Jul"],
        },
        tooltip: {
            fixed: {
                enabled: false,
                position: 'topRight'
            }
        }
      }
      ,
      series: [{
          name: 'Actual Data',
          data: [31, 40, 28, 51, 42, 109, 100]
      }, {
          name: 'Predicted Data',
          data: [33, 38, 35, 47, 38, 90, 91]
      }],
    }
  },
}
</script>