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
        style="position:absolute;width:37%"
        >
        <template v-slot:header="props">
        <q-tr :props="props">
          <q-th v-for="col in props.cols" :key="col.name" :props="props">
            {{ col.label }}
            <p style="margin-top:0;" v-if="(col.label ==='Opening Price') || (col.label ==='Closing Price')">
             {{ yes_date }}
            </p>
            <p style="margin-top:0;" v-if="(col.label ==='Predicted Closing Price')">
             {{ cur_date }}
            </p>
            <p v-else></p>
          </q-th>
        </q-tr>
      </template>
        <!-- style="position:relative;width:100%" -->

        <!-- <template v-if="!isAnalyticsHidden" v-slot:top-right>
        <q-btn
          color="secondary"
          icon-right="analytics"
          label="Generate Prediction"
          no-caps
          @click="addPredictedColumn"
        />
        </template> -->
          <template v-slot:body-cell-actions="props">
            <q-td :props="props">
              <q-btn dense round flat color="grey" @click="deleteRow(props)" icon="remove_circle_outline"></q-btn>
            </q-td>          
          </template>

        </q-table>
      </div>
      <div class="col-4 col-md q-ml-md">
      <q-card  class="my-card text-black"> 
        <!-- v-if="!isHidden" -->
      <q-card-section>
        <div class="text-h6">{{ company_title }}</div>
      </q-card-section>
      <q-card-section>
       <p> {{ company_details }} </p>
      </q-card-section>
      <q-separator dark />
      <q-card-actions>
        <q-btn flat v-if="!isHidden"  @click="medium = true">Bar</q-btn>
        <q-btn flat v-if="!isHidden"  @click="ohlc_chart = true">OHLC</q-btn>
        <!-- <q-btn flat>Action 2</q-btn> -->
      </q-card-actions>
    </q-card>
        <q-dialog v-model="medium">
          <q-card style="width: 900px; max-width: 80vw;">
            <q-card-section>
              <div class="text-h6">{{company_title}}</div>
            </q-card-section>
            <q-card-section class="q-pt-none">
              <Chart/>
            </q-card-section>
            <q-card-actions align="right" class="bg-white text-teal">
              <q-btn flat label="OK" v-close-popup />
            </q-card-actions>
          </q-card>
        </q-dialog>
        <q-dialog v-model="ohlc_chart">
          <q-card style="width: 900px; max-width: 80vw;">
            <q-card-section>
              <div class="text-h6">{{company_title}}  - Open/High/Low/Close Chart</div>
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
            openingPrice: value.openingPrice,
            closingPrice: value.closingPrice,
            predictedPrice: value.predictedPrice
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
      this.company_details = 'EMA12: '+ company[0].emA12 +
                             '-EMA26: ' + company[0].emA26 +
                             '-CMA: ' + company[0].cma +
                             '-MACD: ' + company[0].macd;

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
      

    }

  },
  created(){
    api.get('/getallcompanies')
      .then((response) => {
        console.log(JSON.stringify(response.data))
        this.companies = response.data
      })
      .catch((err) => {
        alert(err)
      })

  },
  data(){
    return {
      cur_date : '10/03/2021',
      yes_date: '09/03/2021',
      company_title:'',
      company_details:'Select a company to see details!',
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
        { name: 'predictedPrice', align: 'center', label: 'Predicted Closing Price', field: 'predictedPrice', sortable: true }
      ],
      data: [
       
      ],
      companies:[
       
      ]
    }
  }
}
</script>
