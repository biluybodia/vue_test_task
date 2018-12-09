<template>
  <v-container column>
    <v-layout row>
      <v-flex xs12>
        <v-text-field prepend-icon="search"
                      v-model="searchItem"
                      solo hide-details single-line></v-text-field>
      </v-flex>
    </v-layout>
    <v-layout row :style="{'border-bottom': '1px solid #fff' }">
      <v-flex xs3
              pt-3
              pb-3
              @click="handleSortList('name')">
        Name
      </v-flex>
      <v-flex xs3
              pt-3
              @click="handleSortList('location')">
        Location
      </v-flex>
      <v-flex xs3
              pt-3
              @click="handleSortList('currency')">
        Currency
      </v-flex>
    </v-layout>
    <v-layout row
              align-center
              v-for="(item, index) in handleCountItems"
              :key="item.id">
      <v-flex xs3>
        <v-text-field
          outline
          v-model="item.name"
          v-if="item.active">
        </v-text-field>
        <span v-else> {{item.name}}</span>
      </v-flex>
      <v-flex xs3>
        <v-text-field
          outline
          v-model="item.location"
          v-if="item.active">
        </v-text-field>
        <span v-else> {{item.location}}</span>
      </v-flex>
      <v-flex xs3>
        <v-text-field
          outline
          @keypress="getOnlyNumber(item.currency, $event)"
          v-model="item.currency"
          v-if="item.active">
        </v-text-field>
        <span v-else> {{item.currency}}</span>
      </v-flex>
      <v-flex xs1>
        <v-btn fab dark small color="cyan"
               @click="handleEditElement(item)">
          <v-icon dark>edit</v-icon>
        </v-btn>
      </v-flex>
      <v-flex xs2
              justify-center>
        <v-btn flat small color="primary" :href="`/${item.id}`">More</v-btn>
      </v-flex>
    </v-layout>
    <v-layout column>
      <v-flex xs12 offset-xs6>
        <div class="user-table__currency-sum">
          {{handleTotalSumm}}
        </div>
      </v-flex>
      <v-flex xs12>
        <v-btn @click="handlePrevPage">Previous</v-btn>
        <v-btn @click="handleNextPage">Next</v-btn>
      </v-flex>
    </v-layout>
  </v-container>
</template>

<script>
  import Vue from 'vue'
  import axios from 'axios'
  Vue.use(axios)
  export default {
    name: 'home-page',
    data () {
      return {
        data: [],
        searchItem: '',
        pageSize: 10,
        currentPage: 1,
        currentSort: 'name',
        currentSortDir: 'asc'
      }
    },
    methods: {
      getData () {
        axios.get('/test.json')
          .then(response => {
            let dataArray = response.data
            dataArray.map(function (item) {
              return (item['active'] = false)
            })
            this.data = dataArray
          })
          .catch(function (error) {
            console.log(error)
          })
      },
      handleSortList: function (item) {
        if (item === this.currentSort) {
          this.currentSortDir = this.currentSortDir === 'asc' ? 'desc' : 'asc'
        }
        this.currentSort = item
      },
      handleNextPage: function () {
        if ((this.currentPage * this.pageSize) < this.data.length) this.currentPage++
      },
      handlePrevPage: function () {
        if (this.currentPage > 1) this.currentPage--
      },
      handleEditElement: function (item) {
        if (item.active) {
          item.active = false
        } else {
          this.data.map(item => {
            return (item.active = false)
          })
          item.active = !item.active
        }
      },
      getOnlyNumber: function (item, $event) {
        let keyCode = ($event.keyCode ? $event.keyCode : $event.which)
        if (((keyCode !== 46) || item.toString().indexOf('.') !== -1) && (keyCode < 48 || keyCode > 57)) {
          $event.preventDefault()
        }
      }
    },
    computed: {
      handleCountItems: function () {
        let getPageElements = this.handleSearchItems.filter((item, index) => {
          let nextPage = (this.currentPage - 1) * this.pageSize
          let prevPage = this.currentPage * this.pageSize
          if (index >= nextPage && index < prevPage) return true
        })
        return getPageElements.sort((a, b) => {
          let modifier = 1
          if (this.currentSortDir === 'desc') modifier = -1
          if (a[this.currentSort] < b[this.currentSort]) return -1 * modifier
          if (a[this.currentSort] > b[this.currentSort]) return modifier
        })
      },
      handleSearchItems: function () {
        let getElements = this.data.filter((item, index) => {
          return item.name.toLowerCase().includes(this.searchItem.toLowerCase()) || item.location.toLowerCase().includes(this.searchItem.toLowerCase()) || item.currency.toString().toLowerCase().includes(this.searchItem.toString().toLowerCase())
        })
        return getElements
      },
      handleTotalSumm: function () {
        return this.data.reduce((accumulator, currentValue) => {
          return +accumulator + +currentValue.currency
        }, 0)
      }
    },
    mounted () {
      this.getData()
    }
  }
</script>

<style lang="scss">
  @import "assets/scss/components/home";
</style>
