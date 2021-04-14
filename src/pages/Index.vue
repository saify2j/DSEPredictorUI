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
    <CompanyList :companies="companiesWithName"
    />
    <div class="row q-mt-md"> 
      <div v-if="isLoaded" class="col-12" style="width:70%;margin-left:10%" >
        <v-select prepend-icon="edit"  :clearable="false" @input='changedValue' :options='companies' label="name" placeholder="Select Companies">
        </v-select>
      </div>
      <q-skeleton v-else style="width:70%;margin-left:10%" class="bg-grey col-12"/>
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
              <ChartOHLC :trading_code = "trading_code"/>
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
      this.company_details = [{name: 'Opening Price: ',value: company[0].yesterdays_closing_price},
                             {name: 'High Price: ', value: company[0].high},
                             {name: 'Low Price: ', value: company[0].low},
                             {name: 'Closing Price: ', value: company[0].closing_price}];

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
    api.get(`/companies`)
      .then((response) => {
        if(response.status === 200){
          this.companiesWithName = JSON.parse(response.data.data);
          //console.log(this.companiesWithName);
          api.get('/predictions')
              .then((response) => {
                //console.log(JSON.stringify(response.data))
                if(response.status === 200){
                    var data = JSON.parse(response.data.data);
                    var date = new Date(data[0].fields.parsed_date);
                    this.yes_date = date.getUTCDate() + '-' + (date.getUTCMonth()+1) + '-' + date.getUTCFullYear();
                    date = new Date(date.getTime() + 1*24*60*60*1000);
                    this.cur_date = date.getUTCDate() + '-' + (date.getUTCMonth()+1) + '-' + date.getUTCFullYear();

                    data.forEach((element) =>
                    {
                      let company = {
                        trading_code: element.fields.trading_code,
                        high: element.fields.high,
                        low: element.fields.low,
                        predicted_next_day_ohlc_price: element.fields.predicted_next_day_ohlc_price,
                        yesterdays_closing_price: element.fields.yesterdays_closing_price,
                        closing_price: element.fields.closing_price,
                        name: "",
                        ohlc: 0,
                        marketChange: 0
                      };                     
                      company.ohlc = this.calcultaeOHLC(company);
                      company.marketChange = this.calcultaeMarketChange(company);

                      //console.log(company.trading_code);
                      let companyName = this.companiesWithName.filter(x => x.fields.trading_code === company.trading_code);
                      //console.log("aasaaa\n"+companyName[0]);
                      if(companyName && companyName.length > 0)
                      {
                        company.name = companyName[0].fields.name +" ("+companyName[0].fields.category+")";
                        this.companies.push(company);
                      }
                      //console.log(company);
                    });

                    this.isLoaded = true;
         }
       })
       .catch((err) => {
         alert(err)
       });
        }
        else
        {
          console.log("data load failed");
        }
      });

  },
  mounted(){
    /*var date = new Date(this.companies[0].parsed_date);
    this.yes_date = date.getUTCDate() + '-' + (date.getUTCMonth()+1) + '-' + date.getUTCFullYear();
    date = new Date(date.getTime() + 1*24*60*60*1000);
    this.cur_date = date.getUTCDate() + '-' + (date.getUTCMonth()+1) + '-' + date.getUTCFullYear();

    this.companies.forEach((company) => {
        company.ohlc = this.calcultaeOHLC(company);
        company.marketChange = this.calcultaeMarketChange(company);
    });*/

    // this.cur_date = new Date(this.yes_date.getDays() + 1);
  },
  data(){
    return {
      cur_date : '',
      yes_date: '',
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
      companiesWithName: [],
      companies:[],
      isLoaded: false
    }
  }
}
</script>
