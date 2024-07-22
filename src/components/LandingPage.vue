<script>
import axios from 'axios'
import OrbitItem from './OrbitItem.vue'
export default {
  components: { OrbitItem },
  data() {
    return {
      orbits: []
    }
  },
  methods: {
    getOrbitsData() {
      const today = new Date()
      const newDate = new Date(today).toISOString().split('T')[0]
      const url = `https://xsrr-l2ye-dpbj.f2.xano.io/api:oUvfVMO5/receive_week?start_date=${newDate}`

      axios.get(url).then((response) => {
        this.orbits = response.data
      })
    }
  },
  mounted() {
    this.getOrbitsData()
  }
}
</script>



<template>
  <div v-if="orbits.length">
    <div v-for="(item, index) in orbits" :key="index">
      <OrbitItem :orbit="item" :orbitIndex="index" :noOfOrbits="orbits.length" />
    </div>
  </div>
</template>

<style scoped>
</style>
