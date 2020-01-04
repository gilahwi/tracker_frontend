<template>
  <div class="past">
    <button
      v-if="this.pastCycles.length > 0"
      class="btn"
      @click="getAverage"
    >Count average length of your cycle</button>
    <h3 v-if="this.average > 0">On average your cycle lasts {{this.average}} days.</h3>
    <div v-for="cycle in pastCycles" v-bind:key="cycle.startDate">
      <p>Started on {{cycle.startDate.split('T')[0]}} and lasted for {{cycle.cycleLength}} days.</p>
    </div>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'Past',
  components: {},
  data() {
    return {
      pastCycles: [],
      average: Number
    }
  },
  methods: {
    getCycles() {
      axios
        .get('http://localhost:3001/api/cycle')
        .then(res => {
          if (res.data.data.ok == 1) {
            this.pastCycles = res.data.data.docs.filter(cycle => cycle.hasEnded)
          } else {
            console.log('Error')
          }
        })
        .catch(err => {
          console.log(err)
        })
    },
    getAverage() {
      let count = 0
      this.pastCycles.forEach(cycle => {
        count += cycle.cycleLength
      })
      this.average = Math.floor(count / this.pastCycles.length)
    }
  },
  created() {
    this.getCycles()
  }
}
</script>

<style scoped>
.past {
  text-align: center;
  justify-content: center;
  display: flex;
  flex-direction: column;
  font-size: 20px;
  color: black;
  padding: 20px;
  background-color: blanchedalmond;
}
.btn {
  padding: 20px;
  font-size: 20px;
  color: black;
  align-self: auto;
  margin: auto;
}
</style>