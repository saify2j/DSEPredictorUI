<template>
  <q-page class="flex flex-center">
    <div class="q-pa-none" style="width:1060px;">
    <div class="row">
      <div id="img-header" class="col-12">
         <img style="width:100%;"
          alt="DSE PREDICTION!"
          src="~assets/back-top.svg"
         >
      </div>
    </div>
    <CompanyList/>
    <div class="row q-mt-md"> 
      <div class="col-12" style="width:70%;margin-left:10%" >
        <v-select prepend-icon="edit"  :clearable="false" @input='changedValue' :options='companies' label="name" placeholder="Select Companies">
        </v-select>
      </div>
    </div>
    <div class="row q-mt-md">
      <div class="col-8">
        <q-table 
        title="Selected Companies"
        :data= 'data'
        :columns='columns'
        row-key="name"
        @row-click="onRowClick"
        style="position:absolute;width:700px;"
        >
          <template v-slot:header="props">
            <q-tr :props="props">
              <q-th v-for="col in props.cols" :key="col.name" :props="props">
              {{ col.label }}
              <p style="margin-top:0;" v-if="(col.label ==='Opening Price') || (col.label ==='Closing Price')">
              {{ yes_date }}
              </p>
              <p style="margin-top:0;" v-if="(col.label ==='Predicted Market Change')">
              {{ cur_date }}
              </p>
              <p v-else></p>
              </q-th>
            </q-tr>
          </template>
          <template v-slot:body-cell-actions="props">
              <q-td :props="props">
                <q-btn dense round flat color="grey" @click="deleteRow(props)" icon="remove_circle_outline"></q-btn>
              </q-td>         
          </template>
          <template v-slot:body-cell-predictedMarketChange="props">
            <q-td :props="props" :class="props.row.predictedMarketChange.includes('-') ? 'text-red':'text-green'">
              {{props.row.predictedMarketChange}}
            </q-td>
          </template>
        </q-table>
      </div>
      <div class="col-4 col-md q-ml-md">
      <q-card  class="my-card text-black"> 
        <!-- v-if="!isHidden" -->
      <q-card-section class="text-teal">
        <div class="text-h6">{{ company_title }}</div>
      </q-card-section>
      <q-card-section>
       <!-- <p> {{ company_details[0].name }} {{ company_details[0].value }}</p> -->
      <ul style="padding-left:8%">
        <li v-for="item in company_details" v-bind:key="item.name">
            <b>{{ item.name }}</b> {{ item.value }}
        </li>
      </ul>
      </q-card-section>
      <q-separator dark />
      <q-card-actions>
        <q-btn flat text-color="secondary" v-if="!isHidden"  @click="medium = true">Bar</q-btn>
        <q-btn flat text-color="secondary" v-if="!isHidden"  @click="ohlc_chart = true">OHLC</q-btn>
        <!-- <q-btn flat>Action 2</q-btn> -->
      </q-card-actions>
    </q-card>
        <q-dialog v-model="medium" style="height:600px;">
          <q-card style="width: 1200px; max-width: 90vw;">
            <q-card-section class="row items-center q-pb-none">
              <div class="text-h6">{{company_title}}</div>
              <q-space />
              <q-btn icon="close" flat round dense v-close-popup />
            </q-card-section>
            <q-card-section class="q-pt-none">
              <Chart :trading_code = "trading_code"
              />
            </q-card-section>
            <q-card-actions align="right" class="bg-white text-teal">
              <q-btn flat label="OK" v-close-popup />
              
            </q-card-actions>
          </q-card>
        </q-dialog>
        <q-dialog v-model="ohlc_chart" style="height:600px;">
          <q-card style="width: 1200px; max-width: 90vw;">
            <q-card-section  class="row items-center q-pb-none">
              <div class="text-h6">{{company_title}}  - Open/High/Low/Close Chart</div>
              <q-space />
              <q-btn icon="close" flat round dense v-close-popup />
            </q-card-section>
            <q-card-section class="q-pt-none">
              <ChartOHLC/>
            </q-card-section>
            <q-card-actions align="right" class="bg-white text-teal">
              <q-btn flat label="OK" v-close-popup />
            </q-card-actions>
          </q-card>
        </q-dialog>
      </div>
    </div>
  </div>
  </q-page>
</template>

<script>
import Chart from './Chart.vue';
import ChartOHLC from './ChartOHLC.vue';
import CompanyList from '../components/CompanyList.vue';
import { api } from 'boot/axios'
export default {
  components: { Chart, ChartOHLC, CompanyList },
  name: 'PageIndex',
  methods: {
    changedValue: function(value) {
      this.select = 0;
      const duplicateValues = this.data.filter(element =>{
        return element.name === value.name
      });
      
      if(duplicateValues.length > 0){
          alert('Already selected!')
          //this.showNotif()
      }
      else {
        this.data.push({
            name: value.name,
            openingPrice: value.yesterdays_closing_price,
            closingPrice: value.closing_price,
            predictedMarketChange: value.marketChange+"%"
          })
        this.data.reverse()
        this.isAnalyticsHidden = false;
      }
     //alert(value)
    },
    onRowClick: function(evt, row){
      // alert(row.name)
      this.isHidden =false;
      this.company_title = row.name;
      var company = this.companies.filter(obj => {
        return obj.name === row.name
      })
      // this.company_details = 'EMA12: '+ company[0].emA12 + 
      //                        '-EMA26: ' + company[0].emA26 +
      //                        '-CMA: ' + company[0].cma +
      //                        '-MACD: ' + company[0].macd;
      this.trading_code = company[0].trading_code;
      this.company_details = [{name: 'EMA12: ',value: company[0].emA12},
                             {name: 'EMA26: ', value: company[0].emA26},
                             {name: 'SMA: ', value: company[0].cma},
                             {name: 'MACD: ', value: company[0].macd}];

    },
    deleteRow: function(props){
      this.data.pop(props.row)
    },
    showNotif: function(){
      this.$q.notify.setDefaults({
      position: 'top-right',
      timeout: 10,
      textColor: 'white',
      actions: [{ icon: 'close', color: 'white' }]
      })
      this.$q.notify({
        message: 'Test Notify',
        icon: 'announcement'
      })
      

    },
    calcultaeOHLC(company){
        var o = parseFloat(company.yesterdays_closing_price);
        var h = parseFloat(company.high);
        var l = parseFloat(company.low);
        var c = parseFloat(company.closing_price);
        var ohlc = (o+h+l+c)/4;
        return ohlc;
    },
    calcultaeMarketChange(company){
        var marketChange =((parseFloat(company.predicted_next_day_ohlc_price) - company.ohlc)/company.ohlc)*100;
        marketChange = Math.round(marketChange*100)/100;
        return marketChange;
    }


  },
  created(){
     /*api.get('/predictions')
       .then((response) => {
         console.log(JSON.stringify(response.data))
         if(response.status === 200){
            this.companies = JSON.parse(response.data.data);
            var date = new Date(this.companies[0].parsed_date);
            this.yes_date = date.getUTCDate() + '-' + (date.getUTCMonth()+1) + '-' + date.getUTCFullYear();
            date = new Date(date.getTime() + 1*24*60*60*1000);
            this.cur_date = date.getUTCDate() + '-' + (date.getUTCMonth()+1) + '-' + date.getUTCFullYear();
         }
         //this.companies = response.data
       })
       .catch((err) => {
         alert(err)
       })*/

  },
  mounted(){
    var date = new Date(this.companies[0].parsed_date);
    this.yes_date = date.getUTCDate() + '-' + (date.getUTCMonth()+1) + '-' + date.getUTCFullYear();
    date = new Date(date.getTime() + 1*24*60*60*1000);
    this.cur_date = date.getUTCDate() + '-' + (date.getUTCMonth()+1) + '-' + date.getUTCFullYear();

    this.companies.forEach((company) => {
        company.ohlc = this.calcultaeOHLC(company);
        company.marketChange = this.calcultaeMarketChange(company);
    });

    // this.cur_date = new Date(this.yes_date.getDays() + 1);
  },
  data(){
    return {
      cur_date : '10/03/2021',
      yes_date: '09/03/2021',
      company_title:'',
      trading_code: '',
      company_details:[{name: 'Select a company from the table to see more details!', value: null}],
      isHidden: true,
      isAnalyticsHidden: true,
      medium: false,
      ohlc_chart: false,
      columns: [
        { name: 'actions', label: 'Action', field: 'actions', align:'center' },
        {
          name: 'name',
          required: true,
          label: 'Company Name',
          align: 'left',
          field: row => row.name,
          format: val => `${val}`
          
        },
        { name: 'openingPrice', align: 'center', label: 'Opening Price', field: 'openingPrice', sortable: true, headerStyle: 'width: 110px' },
        { name: 'closingPrice', align: 'center', label: 'Closing Price', field: 'closingPrice', sortable: true },
        { 
          name: 'predictedMarketChange',
          align: 'center',
          label: 'Predicted Market Change', 
          field: 'predictedMarketChange',
          sortable: true 
        }
      ],
      data: [
        
      ],
      companies:[
        {
          trading_code: "1JANATAMF",
          parsed_date: "2021-04-12T18:00:00.000+00:00",
          last_traded_price: "5.100",
          high : "5.100",
          low: "4.900",
          closing_price: "4.900",
          predicted_next_day_ohlc_price: "4.993",
          yesterdays_closing_price:"5.100",
          change: "4.900",
          trade: 67,
          value_mn: "1.391",
          volume: "277458.000",
          name: "Comapany A",
          emA12: 1,
          emA26: 2,
          cma: 3,
          macd: 4,
          ohlc: 0,
          marketChange: 0
        },
        {
          trading_code: "AAMRANET",
          parsed_date: "2021-04-12T18:00:00.000+00:00",
          last_traded_price: "5.100",
          high : "5.100",
          low: "4.900",
          closing_price: "4.900",
          predicted_next_day_ohlc_price: "4.993",
          yesterdays_closing_price:"4.5",
          change: "4.900",
          trade: 67,
          value_mn: "1.391",
          volume: "277458.000",
          name: "Comapany B",
          emA12: 1,
          emA26: 2,
          cma: 3,
          macd: 4,
          ohlc: 0,
          marketChange: 0
        },
        {
          trading_code: "AAMRATECH",
          parsed_date: "2021-04-12T18:00:00.000+00:00",
          last_traded_price: "5.100",
          high : "5.100",
          low: "4.900",
          closing_price: "4.900",
          predicted_next_day_ohlc_price: "4.993",
          yesterdays_closing_price:"5.100",
          change: "4.900",
          trade: 67,
          value_mn: "1.391",
          volume: "277458.000",
          name: "Comapany C",
          emA12: 1,
          emA26: 2,
          cma: 3,
          macd: 4,
          ohlc: 0,
          marketChange: 0
        },
        {
          trading_code: "ABB1STMF",
          parsed_date: "2021-04-12T18:00:00.000+00:00",
          last_traded_price: "5.100",
          high : "5.100",
          low: "4.900",
          closing_price: "4.900",
          predicted_next_day_ohlc_price: "4.993",
          yesterdays_closing_price:"5.100",
          change: "4.900",
          trade: 67,
          value_mn: "1.391",
          volume: "277458.000",
          name: "Comapany D",
          emA12: 1,
          emA26: 2,
          cma: 3,
          macd: 4,
          ohlc: 0,
          marketChange: 0
        }, 
      ]
    }
  }
}
</script>
