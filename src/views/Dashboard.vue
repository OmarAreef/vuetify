<template>
  <v-container class="mb-6 pa-2 pat-5">
    <v-row justify="center" class="mb-6 pa-2">
      <v-col cols="5" class="text-center">
        <h1 class="text-h4 white--text lighten-5--text">
          Welcome to your <br />
        </h1>
        <h1 class="text-h2 white--text lighten-1--text">Dashboard</h1>
      </v-col>
    </v-row>
    <v-row justify="start">
      <v-col cols="3" of justify="center">
        <v-btn @click="create1" class="rounded-pill" v-if="this.role == 'bank'">
          create new fund/ loan
        </v-btn>
      </v-col>
      <v-col cols="3" class="offset-5" justify="center">
        <v-btn @click="create" class="rounded-pill" v-if="this.role != 'bank'">
          create new application
        </v-btn>
      </v-col>
    </v-row>
    <v-overlay :value="overlay" opacity="0.9" v-if="this.role != 'bank'">
      <application-form @close-overlay="close" :funds="funds" />
    </v-overlay>
    <v-overlay :value="overlay1" opacity="0.9" v-if="this.role == 'bank'">
      <funds-form @close-overlay="close1" />
    </v-overlay>
    <h1
      class="text-h4 white--text lighten-1--text text-center"
      justify="center"
    >
      {{
        role === "customer"
          ? "Loans"
          : role === "bank"
          ? "Applications"
          : "Funds"
      }}
    </h1>
    <v-row justify="center">
      <v-col v-if="role === 'bank'" cols="2">
        <report-comp ref="reportComp"></report-comp>
      </v-col>
      <v-col cols="10">
        <loan-item
          v-for="(item, index) in loans"
          :key="index"
          :loan="item"
          @refresh="refresh"
        />
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
import axios from "axios";
import LoanItem from "../components/LoanItem.vue";
import ApplicationForm from "../components/ApplicationForm.vue";
import FundsForm from "../components/FundsForm.vue";
import ReportComp from "../components/ReportComp.vue";
export default {
  name: "Dashboard",
  components: { LoanItem, ApplicationForm, FundsForm, ReportComp },
  data: () => ({
    loans: [],
    funds: [],
    model: 0,
    role: sessionStorage.getItem("Role"),
    overlay: false,
    overlay1: false,
  }),
  methods: {
    create() {
      this.overlay = true;
    },
    create1() {
      this.overlay1 = true;
    },
    close() {
      this.overlay = false;
      axios
        .get("http://127.0.0.1:8000/app/loan/", {
          headers: {
            token: sessionStorage.getItem("Token"),
          },
        })
        .then((response) => {
          this.loans = response.data["data"];
          //console.log(this.loans);
        })
        .catch((error) => {
          console.error(error.response.data.message);
        });
    },
    close1() {
      this.overlay1 = false;
    },
    refresh() {
      this.$refs.reportComp.getReport();
      axios
        .get("http://127.0.0.1:8000/app/loan/", {
          headers: {
            token: sessionStorage.getItem("Token"),
          },
        })
        .then((response) => {
          this.loans = response.data["data"];
          //console.log(this.loans);
        })
        .catch((error) => {
          console.error(error.response.data.message);
        });
    },
  },
  mounted() {
    axios
      .get("http://127.0.0.1:8000/app/loan/", {
        headers: {
          token: sessionStorage.getItem("Token"),
        },
      })
      .then((response) => {
        this.loans = response.data["data"];
        //console.log(this.loans);
      })
      .catch((error) => {
        console.error(error.response.data.message);
      });
    axios
      .get("http://127.0.0.1:8000/app/getfund/", {
        headers: {
          token: sessionStorage.getItem("Token"),
        },
      })
      .then((response) => {
        this.funds = response.data["data"];
      })
      .catch((error) => {
        console.error(error.response.data.message);
      });
  },
};
</script>
