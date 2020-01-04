<template>
  <div class="home">
    <div class="caution">
      <button @click="changeCycle" class="btn">Start new cycle</button>
      <p>Only press this button</p>
      <p>on the day when your</p>
      <p>period/new cycle starts.</p>
      <p>Otherwise,</p>
      <p>give a date manually:</p>
      <form @submit="manualChange">
        <input type="text" name="manualCycle" placeholder="31/5/2019" />
        <input type="submit" value="Save" class="btn" />
      </form>
    </div>
    <br />
    <div class="info" v-if="this.currentCycle.startDate">
      <p>This cycle started on {{this.currentCycle.startDate.split('T')[0]}}.</p>
      <p>It's day {{this.currentCycle.cycleLength}} of your cycle.</p>
    </div>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'Home',
  components: {},
  data() {
    return {
      currentCycle: {}
    }
  },
  methods: {
    manualChange(manualDate) {
      const day = manualDate.split('/')[0]
      const month = manualDate.split('/')[1]
      const year = manualDate.split('/')[2]
      const givenDate = new Date(year, month, day)

      this.endCycle(givenDate)
      this.startNewCycle(givenDate)
    },
    startNewCycle(startDate = new Date(), hasEnded = false, cycleLength = 1) {
      // const startDate = new Date()
      // const hasEnded = false
      // const cycleLength = 1

      axios
        .post('http://localhost:3001/api/cycle', {
          startDate,
          hasEnded,
          cycleLength
        })
        .catch(err => console.log(err))
    },
    endCycle() {
      // get from mongoDB the only cycle for which
      // the property hasEnded is false
      axios
        .get('http://localhost:3001/api/cycle')
        .then(res => {
          if (res.data.data.ok == 1) {
            this.currentCycle = res.data.data.docs.filter(
              cycle => !cycle.hasEnded
            )[0]
          } else {
            console.log('Error')
          }
        })
        .catch(err => {
          console.log(err)
        })

      // count the difference between startDate and
      // the date now => cycleLength
      const now = Date.parse(new Date())
      const then = Date.parse(this.currentCycle.startDate)
      const difference = Math.floor((now - then) / 86400000)

      // modify the same document in mongoDB =>
      // hasEnded: true, cycleLength: difference
      axios
        .put('http://localhost:3001/api/cycle', {
          difference
        })
        .catch(err => console.log(err))
    },
    changeCycle() {
      this.endCycle()
      this.startNewCycle()
    }
  },
  created() {
    //viewDetails()
    axios
      .get('http://localhost:3001/api/cycle')
      .then(res => {
        if (res.data.data.ok == 1) {
          this.currentCycle = res.data.data.docs.filter(
            cycle => !cycle.hasEnded
          )[0]

          let today = Date.parse(new Date())
          let startDay = Date.parse(this.currentCycle.startDate)
          this.currentCycle.cycleLength = Math.floor(
            (today - startDay + 86400000) / 86400000
          )
        }
      })
      .catch(err => console.log(err))
  }
}
</script>

<style scoped>
.home {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
  text-align: center;
  justify-content: center;
  display: flex;
  flex-direction: row;
  font-size: 20px;
  color: black;
  height: 100vh;
}
.info {
  background: aquamarine;
  width: 50%;
  text-align: center;
  justify-content: center;
  padding: 50px;
  flex-grow: 1;
}
.caution {
  background: hotpink;
  font-size: 14px;
  width: 50%;
  text-align: center;
  justify-content: center;
  padding: 50px;
  flex-grow: 1;
}
.btn {
  padding: 20px;
  font-size: 20px;
  color: black;
  align-self: auto;
  margin: auto;
}
</style>
