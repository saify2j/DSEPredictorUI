<template>
  <q-page class="flex flex-center">
    <!-- <img
      alt="Quasar logo"
      src="~assets/quasar-logo-full.svg"
    > -->
    <div class="q-pa-lg" style="width:900px;">
    <div class="row items-center">
      <div class="col-8">
        <v-select :clearable="false" @input='changedValue' :options='companies' label="name" placeholder="Select Companies"></v-select>
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
        />
      </div>
      <div class="col-4 col-md q-ml-md">
      <q-card v-if="!isHidden" class="my-card bg-teal-2 text-black">
      <q-card-section>
        <div class="text-h6">{{ company_title }}</div>
      </q-card-section>
      <q-card-section>
       <p> {{ company_details }} </p>
      </q-card-section>

      <q-separator dark />

      <q-card-actions>
        <q-btn flat>Show Details</q-btn>
        <!-- <q-btn flat>Action 2</q-btn> -->
      </q-card-actions>
    </q-card>
      </div>
    </div>

  </div>
  </q-page>
</template>

<script>
export default {
  name: 'PageIndex',
  methods: {
    changedValue: function(value) {
      this.select = 0;
    //receive the value selected (return an array if is multiple)
     this.data.push({
          name: value.name,
          openingPrice: value.openingPrice,
          closingPrice: value.closingPrice,
        })
     //alert(value)
  },
  onRowClick: function(evt, row){
    // alert(row.name)
    this.isHidden =false;
    this.company_title = row.name;
    this.company_details = 'Opening Price: '+ row.openingPrice +
                           'Closing Price: ' + row.closingPrice;

  }
  },
  data(){
    return {
      company_title:'',
      company_details:'',
      isHidden: true,
      columns: [
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
          name: 'City Bank',
          openingPrice: '1110',
          closingPrice: '1120',
        },
      ]
    }
  }
}
</script>
