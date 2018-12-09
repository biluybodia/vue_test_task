<template>
  <div>
    {{item}}
  </div>
</template>
<script>
  import Vue from 'vue'
  import axios from 'axios'
  Vue.use(axios)

  export default {
    data () {
      return {
        id: 0,
        item: {}
      }
    },
    methods: {
      getData () {
        axios.get('/test.json')
          .then(response => {
            this.item = response.data.filter((item) => {
              return item.id === this.getId
            })
          })
      }
    },
    computed: {
      getId () {
        return this.$route.params.id
      }
    },
    mounted () {
      this.getData()
    }
  }
</script>

<style lang="scss"></style>
