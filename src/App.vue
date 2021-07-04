<template>
  <div class="App">
    <label for="amount">Kreditsumme in €</label>
    <input v-model="amount" name="amount" type="text">
    <label for="duration">Laufzeit in Jahren</label>
    <input v-model="duration" name="duration" type="text">
    <label for="interest">Zins pa</label>
    <input v-model="interest" name="interest" type="text">
    <button @click="calculate">Berechnen</button>
<table style="width:50%">
  <tr>
    <th>Jahr</th>
    <th>Rückzahlen</th>
    <th>Zins</th>
    <th>Restbetrag</th>
    <th>Monatliche Rate</th>
  </tr>
  <tr v-for="(result, index) in results" :key="`tr-${index}`">
    <td>{{ index + 1 }}</td>
    <td>{{ result.repayment.toFixed(2) }}</td>
    <td>{{ result.yearlyInterest.toFixed(2) }}</td>
    <td>{{ result.remaining.toFixed(2) }}</td>
    <td>{{ (result.annuity / 12).toFixed(2) }}</td>
  </tr>
</table>
<line-chart :chartdata="interestData"/>
  </div>
</template>

<script>
import interestData from './interestData'
// import LineChart from './components/LineChart.vue'
// import Test from './components/Test.vue'

export default {
  components: {
  },
  data() {
    return {
      amount: 640000,
      duration: 35,
      interest: 0.3,
      results: []
    };
  },
  computed: {
    interestTimeData() {
      const interestPerYear = [];
      interestData.forEach((data) => {
        console.log(data);
        for (let i = 0; i < data.duration; i++) {
          interestPerYear.push(data.interest); 
        }
      })
      console.log(interestPerYear);
      return interestPerYear;
    }
  },
  methods: {
    calculate() {
      console.log(this.interestTimeData[0]);
      this.results = []
      let remaining = this.amount;
      let annuity = this.calculateAnnuity(this.amount, this.duration, this.interestTimeData[0]);
      for (let i = 0; i < this.duration; i++) {
        const interest = this.interestTimeData[i];
          if(i !== 0 && this.interestTimeData[i-1] !== interest) {
            annuity = this.calculateAnnuity(remaining, this.duration - i, interest);
          }
          const yearlyInterest = remaining * ( interest / 100 );
          const repayment = annuity - yearlyInterest;
          remaining -= repayment;
        this.results.push({
          yearlyInterest,
          repayment,
          remaining,
          annuity
        })
      }
    },
    calculateAnnuity(amount, duration, interest) {
      const realInterest = interest / 100;
      const interestFactor = (1 + realInterest)**duration;
      const inverseInterest = (interestFactor - 1);
      const annuityFactor = ( interestFactor * realInterest ) / inverseInterest;
      return annuityFactor * amount;
    }
  }
};
</script>

<style>
th {
  text-align: left;
}
</style>
