<template>
      <div class="row" style="margin-top:-80px;">
        <div style="margin-left:5%" class="col-2"><q-btn push color="white" @click="fixed = true, category_name = 'A'" text-color="secondary" label="Category A" /></div>
        <div class="col-2"><q-btn push color="white" text-color="secondary"  label="Category B" @click="fixed = true, category_name = 'B'" /></div>
        <div class="col-2"><q-btn push color="white" text-color="secondary" label="Category N" @click="fixed = true, category_name = 'N'" /></div>
        <div class="col-2"><q-btn push color="white" text-color="secondary" label="Category G" @click="fixed = true, category_name = 'G'" /></div>
        <div class="col-2"><q-btn push color="white" text-color="secondary" label="Category Z" @click="fixed = true, category_name = 'Z'" /></div>
        <q-dialog v-model="fixed">
            <q-card v-if="this.companies.length > 0">
                <q-card-section>
                    <div class="text-h6">List of companies in Category {{ category_name }}</div>
                </q-card-section>
                <q-separator />
                <q-card-section style="width:500px;max-height: 50vh" class="scroll">
                    <p v-for="company in loadCompanies(category_name)" :key="company.pk">{{company.fields.name}}</p>
                </q-card-section>
                <q-separator />
                <q-card-actions align="right">
                    <q-btn flat label="Ok" color="secondary" v-close-popup />
                </q-card-actions>
            </q-card>
            <q-skeleton v-else width="400px" class="bg-grey"/>
        </q-dialog>
      </div>
</template>
<script>
import { api } from 'boot/axios'
export default {
  name: 'CompanyList',
  props: ['companies'],
  data () {
    return {
      fixed: false,
      category_name:'',
      isLoaded: false,
      companiesOld: [
        {
            model: "main",
            pk: "1",
            fields: {
                name: "amarnet",
                trading_code: "asadsada",
                sector: "asdasdas",
                category: "A"
            }
        },
        {
            model: "main",
            pk: "12",
            fields: {
                name: "amarnet2",
                trading_code: "asadsada",
                sector: "asdasdas",
                category: "A"
            }
        },
        {
            model: "main",
            pk: "13",
            fields: {
                name: "amarnet3",
                trading_code: "asadsada",
                sector: "asdasdas",
                category: "B"
            }
        },
        {
            model: "main",
            pk: "143",
            fields: {
                name: "amarnet4",
                trading_code: "asadsada",
                sector: "asdasdas",
                category: "B"
            }
        },
        {
            model: "main",
            pk: "133",
            fields: {
                name: "amarnet5",
                trading_code: "asadsada",
                sector: "asdasdas",
                category: "B"
            }
        },
    ],
    }
  },
  methods: {
      loadCompanies(category){
        //console.log(this.companies.filter(x => x.fields.category === category)) 
        if(this.companies)
          this.isLoaded = true;
        return this.companies.filter(x => x.fields.category === category);
      },
    },
  mounted () {
    /*api.get(`/companies`)
      .then((response) => {
        if(response.status === 200){
          this.companies = JSON.parse(response.data.data);
          //console.log(this.companies);
          this.isLoaded = true;
        }
        else
        {
          console.log("data load failed");
        }
      });*/
  }
}
</script>
