<template>
  <q-page class="flex flex-center">
    <div class="q-pa-none" style="width:960px;">
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
        style="position:relative;width:100%"
        >
        <template v-if="!isAnalyticsHidden" v-slot:top-right>
        <q-btn
          color="secondary"
          icon-right="analytics"
          label="Generate Prediction"
          no-caps
          @click="addPredictedColumn"
        />
        </template>
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
export default {
  components: { Chart, ChartOHLC, CompanyList },
  name: 'PageIndex',
  methods: {
    changedValue: function(value) {
      this.select = 0;
      if(this.data.includes(value)){
          alert('Already selected!')
      }
      else {
        this.data.push({
            name: value.name,
            openingPrice: value.openingPrice,
            closingPrice: value.closingPrice,
          })
        this.isAnalyticsHidden = false;
      }
     //alert(value)
    },
    onRowClick: function(evt, row){
      // alert(row.name)
      this.isHidden =false;
      this.company_title = row.name;
      this.company_details = 'Opening Price: '+ row.openingPrice +
                            'Closing Price: ' + row.closingPrice;

    },
    addPredictedColumn: function(){
      this.columns.push(
        { name: 'predictedPrice', align: 'center', label: 'Predicted Closing Price', field: 'predictedPrice', sortable: true }
      )
      this.isAnalyticsHidden = true;
    },
    deleteRow: function(props){
      this.data.pop(props.row)
    }
  },
  data(){
    return {
      company_title:'',
      company_details:'Select a company to see details!',
      isHidden: true,
      isAnalyticsHidden: true,
      medium: false,
      ohlc_chart: false,
      columns: [
        { name: 'actions', label: 'Remove', field: '', align:'center' },
        {
          name: 'name',
          required: true,
          label: 'Company Name',
          align: 'left',
          field: row => row.name,
          format: val => `${val}`,
          sortable: true
        },
        { name: 'openingPrice', align: 'center', label: 'Opening Price', field: 'openingPrice', sortable: true },
        { name: 'closingPrice', label: 'Closing Price', field: 'closingPrice', sortable: true },
      ],
      data: [
       
      ],
      companies:[
        {
          name: 'Beximco',
          openingPrice: '1210',
          closingPrice: '1220',
        },
        {
          name: 'A Company',
          openingPrice: '1110',
          closingPrice: '1120',
        },
        {
          name: 'B Company',
          openingPrice: '1110',
          closingPrice: '1120',
        },
        {
          name: 'C Company',
          openingPrice: '1110',
          closingPrice: '1120',
        },
        {
          name: 'D Company',
          openingPrice: '1110',
          closingPrice: '1120',
        },
        {
          name: 'E Company',
          openingPrice: '1110',
          closingPrice: '1120',
        },
        {
          name: 'F Company',
          openingPrice: '1110',
          closingPrice: '1120',
        },
        {
          name: 'G Company',
          openingPrice: '1110',
          closingPrice: '1120',
        },
      ]
    }
  }
}
</script>
