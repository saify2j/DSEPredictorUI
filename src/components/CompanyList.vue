<template>
      <div class="row" style="margin-top:-80px;">
        <div style="margin-left:5%" class="col-2"><q-btn push color="white" @click="fixed = true, category_name = 'Category A'" text-color="secondary" label="Category A" /></div>
        <div class="col-2"><q-btn push color="white" text-color="secondary"  label="Category B" @click="fixed = true, category_name = 'Category B'" /></div>
        <div class="col-2"><q-btn push color="white" text-color="secondary" label="Category N" @click="fixed = true, category_name = 'Category N'" /></div>
        <div class="col-2"><q-btn push color="white" text-color="secondary" label="Category G" @click="fixed = true, category_name = 'Category G'" /></div>
        <div class="col-2"><q-btn push color="white" text-color="secondary" label="Category Z" @click="fixed = true, category_name = 'Category Z'" /></div>
        <q-dialog v-model="fixed">
            <q-card>
                <q-card-section>
                    <div class="text-h6">List of companies in {{ category_name }}</div>
                </q-card-section>
                <q-separator />
                <q-card-section style="width:500px;max-height: 50vh" class="scroll">
                    <p v-for="n in 15" :key="n">Company {{ n }}</p>
                </q-card-section>
                <q-separator />
                <q-card-actions align="right">
                    <q-btn flat label="Ok" color="secondary" v-close-popup />
                </q-card-actions>
            </q-card>
        </q-dialog>
      </div>
</template>
<script>
export default {
  name: 'CompanyList',
  data () {
    return {
      fixed: false,
      category_name:'',
    }
  }
}
</script>
