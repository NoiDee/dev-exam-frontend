<template>
  <v-container>
    <v-row>
      <v-col cols="12" class="text-center">
        <v-btn elevation="2" @click="ping" >Ping!</v-btn>
      </v-col>
      <v-col cols="12">
        <v-data-table
          :headers="headers"
          :items="tableData"
          :items-per-page="5"
          class="elevation-1"
          :loading="isLoading"
          :sort-by="['date_time']"
          :sort-desc="[true]"
        >
          <template v-slot:body="{ items }">
            <tbody>
              <tr v-for="item in items" :key="item.response_id" >
                <td>
                  {{ formatDate(item.date_time) }}
                </td>
                <td>
                  <div v-bind:key="name" v-for="(value, name) in item.http_response">
                    <strong>{{ name }}</strong>: {{ value }}
                  </div>
                </td>
              </tr>
            </tbody>
          </template>
        </v-data-table>
      </v-col>
    </v-row>
    <v-snackbar
      v-model="snackShow"
      :multi-line="true"
      :timeout="5000"
    >
      {{ snackText }}
      <template v-slot:action="{ attrs }">
        <v-btn
          color="red"
          text
          v-bind="attrs"
          @click="snackShow = false"
        >
          Close
        </v-btn>
      </template>
    </v-snackbar>
  </v-container>
</template>

<script>
import axios from 'axios'
import moment from 'moment'
export default {

  data () {
    return {
      headers: [
        {
          text: 'Date and Time',
          value: 'date_time'
        },
        { text: 'HTTP Response', value: 'http_response' }
      ],
      isLoading: true,
      tableData: [],
      snackShow: false,
      snackText: ''
    }
  },
  beforeMount () {
    this.loadFromApi()
  },
  methods: {
    formatDate (value) {
      if (value) {
        return moment(String(value)).format('YYYY/MM/DD HH:mm:ss')
      }
    },
    showMessage: function (msg) {
      this.snackText = msg
      this.snackShow = true
    },
    renderData: function (item) {
      return {
        ...item,
        http_response: JSON.stringify(item.http_response)
      }
    },
    loadFromApi: async function () {
      try {
        const response = await axios.get('http://localhost/api/response')
        this.tableData = response.data
      } catch (error) {
        console.error(error)
        this.showMessage('Failed to load data.')
      } finally {
        this.isLoading = false
      }
    },
    ping: function () {
      const self = this
      self.isLoading = true
      self.showMessage('Pinging the server, please wait...')
      axios
        .post('http://localhost/api/response')
        .then(function (response) {
          self.tableData.push(response.data)
          self.snackShow = false
        })
        .catch(function (error) {
          console.error(error)
          self.showMessage('Failed to load data.')
        })
        .finally(function () {
          self.isLoading = false
        })
    }
  }
}
</script>
