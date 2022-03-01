<template>
  <v-card style="min-width: 650px" class="fill-height">
    <v-card-title>
      <v-row>
        <v-col cols="5"
          ><h1 class="text-display-4">Loan#: {{ loan.id }}</h1>
        </v-col>
        <v-col cols="3" class="offset-3 d-flex justify-end" justify="end">
          <v-btn small color="red" class="ml-auto" @click="close">close</v-btn>
        </v-col>
      </v-row>
    </v-card-title>
    <v-row class="mt-1">
      <v-col cols="5 " class="text-center pa-1">
        <h1 class="text-display-6">Loan summary</h1>
      </v-col>
    </v-row>
    <v-row class="mt-0">
      <v-col cols="3" class="text-end pa-1">
        <p class="text-body-2">principal amount:</p>
      </v-col>
      <v-col cols="2" class="text-start pa-1">
        <p class="text-body-2">{{loan.principal}}</p>
      </v-col>
      <v-col cols="3" class="text-end pa-1">
        <p class="text-body-2">Total amount:</p>
      </v-col>
      <v-col cols="2" class="text-start pa-1">
        <p class="text-body-2">{{loan.amount}}</p>
      </v-col>
    </v-row>
    <v-row class="mt-0">
      <v-col cols="3" class="text-end pa-1">
        <p class="text-body-2">Annual Rate:</p>
      </v-col>
      <v-col cols="4" class="text-start pa-1">
        <p class="text-body-2">{{ loan.fund.rate }}%</p>
      </v-col>
    </v-row>
    <v-row class="mt-0">
      <v-col cols="3" class="text-end pa-1">
        <p class="text-body-2">Duration:</p>
      </v-col>
      <v-col cols="2" class="text-start pa-1">
        <p class="text-body-2">{{ loan.fund.duration }} years</p>
      </v-col>
      <v-col cols="3" class="text-end pa-1">
        <p class="text-body-2">Payements in a year:</p>
      </v-col>
      <v-col cols="2" class="text-start pa-1">
        <p class="text-body-2">1</p>
      </v-col>
    </v-row>
    <v-simple-table style="overflow-y: scroll; max-height:300px">
      <template v-slot:default>
        <thead>
          <tr>
            <th class="text-left">Year</th>
            <th class="text-left">Beginning balance</th>
            <th class="text-left">Payement</th>
            <th class="text-left">Interest</th>
            <th class="text-left">Total</th>
            <th class="text-left">Ending balance</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(item, index) in LoanList" :key="index">
            <td>{{ item.index }}</td>
            <td>{{ item.Begin }}</td>
            <td>{{ item.yearPay }}</td>
            <td>{{ item.interest }}</td>
            <td>{{ item.total }}</td>
            <td>{{ item.end }}</td>
          </tr>
        </tbody>
      </template>
    </v-simple-table>
  </v-card>
</template>
<script>
export default {
  data: function () {
    return {
      myAmount: this.loan.principal,
      yearlyAmount: this.loan.principal / this.loan.fund.duration,
      interest: this.loan.principal * (this.loan.fund.rate / 100),
      years: this.loan.fund.duration,
    };
  },
  methods: {
    close() {
      this.$emit("close-table");
    },
  },
  computed: {
    LoanList() {
      let list = [];
      let amount = this.myAmount;

      for (let i = 0; i < this.years; i++) {
        let myObj = {};
        myObj["index"] = i + 1;
        myObj["Begin"] = amount;
        myObj["yearPay"] = parseInt(this.yearlyAmount);
        myObj["interest"] = parseInt(this.interest);
        myObj["total"] = parseInt(this.yearlyAmount + this.interest);
        amount = parseInt(amount - (this.yearlyAmount));
        myObj["end"] = amount

        list.push(myObj);
      }

      return list;
    },
  },

  props: ["loan"],
};
</script>
