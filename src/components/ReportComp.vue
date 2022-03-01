<template>
  <v-card class="rounded-lg" style="min-width: 300px">
    <v-simple-table>
      <template v-slot:default>
        <thead>
          <tr>
            <th class="text-left">Loans count</th>
            <th class="text-left">Loans sum</th>
          </tr>
        </thead>
        <tbody>
          <tr>
            <td>{{ myData.loans }}</td>
            <td>{{ myData.loansSum }}</td>
          </tr>
        </tbody>
      </template>
    </v-simple-table>
    <v-simple-table>
      <template v-slot:default>
        <thead>
          <tr>
            <th class="text-left">funds count</th>
            <th class="text-left">funds sum</th>
          </tr>
        </thead>
        <tbody>
          <tr>
            <td>{{ myData.funds }}</td>
            <td>{{ myData.fundsSum }}</td>
          </tr>
        </tbody>
      </template>
    </v-simple-table>
    <v-simple-table>
      <template v-slot:default>
        <thead>
          <tr>
            <th class="text-left">Loan Applications</th>
            <th class="text-left">Fund Applications</th>
          </tr>
        </thead>
        <tbody>
          <tr>
            <td>{{ myData.loanApplications }}</td>
            <td>{{ myData.fundApplications }}</td>
          </tr>
        </tbody>
      </template>
    </v-simple-table>
  </v-card>
</template>
<script>
import axios from "axios";
export default {
  data: () => ({
    myData: {},
  }),
  methods: {
    getReport() {
      axios
        .get("http://127.0.0.1:8000/app/reports/", {
          headers: {
            token: sessionStorage.getItem("Token"),
          },
        })
        .then((response) => {
          console.log(response.data);
          this.myData = response.data;
        })
        .catch((error) => {
          console.log(error);
        });
    },
  },
  mounted() {
     this.getReport()
  },
};
</script>
