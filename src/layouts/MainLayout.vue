<template>
  <q-layout view="lHh Lpr lFf">
    <q-header elevated>
      <q-toolbar class="bg-white text-teal">
        <q-toolbar-title>
          DSE Prediction!<br>
          <p style="font-size:12px;">by BSSE09</p>
        </q-toolbar-title>
      </q-toolbar>
    </q-header>
    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script>

export default {
  name: 'MainLayout',
  // data () {
  //   return {
      
  //   }
  // }
}
</script>
