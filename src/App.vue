<template>
  <v-app>
    <v-app-bar
      app
      color="primary"
      dark
    >
      <div class="d-flex align-center">
        <h1>Dev Exam</h1>
      </div>
    </v-app-bar>

    <v-main>
      <PingTable/>
    </v-main>
  </v-app>
</template>

<script>
import PingTable from './components/PingTable'

export default {
  name: 'App',

  components: {
    PingTable
  }
}
</script>
