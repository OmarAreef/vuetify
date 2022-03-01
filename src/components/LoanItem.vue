<template>
  <v-container class="p-1 mt-2">
    <v-row align="center" justify="center">
      <v-col cols="5" class="offset-3 pa-1 mb-0">
        <v-alert
          v-if="alert"
          color="red"
          v-model="alert"
          dismissible
          elevation="8"
          shaped
          type="error"
          class="text-caption"
        >
          {{ this.message }}
        </v-alert>
      </v-col>
    </v-row>
    <v-row align="center" justify="center">
      <v-col cols="5" class="offset-3 pa-1 mb-0">
        <v-alert
          v-if="success"
          color="success"
          v-model="success"
          dismissible
          elevation="8"
          shaped
          type="success"
          class="text-caption"
          >{{ this.message }}
        </v-alert>
      </v-col>
    </v-row>
    <v-row class="pat-2">
      <v-col cols="9" class="pa-2 offset-2">
        <v-card class="grey lighten-3 rounded-pill pa-2">
          <v-card-title
            class="pa-2 pl-3 ma-n1 ml-2"
            align="center"
            justify="center"
          >
            <h1 class="font-weight-medium text-h6 ml-5 mb-1" v-if="!loan.accepted">
              Application #: {{ loan.id }}
            </h1>
            <h1 class="font-weight-medium text-h6 ml-5 mb-1" v-if="loan.accepted">
              Loan #: {{ loan.id }}
            </h1>
            <v-btn
              class="ml-auto mr-3 mb-2 rounded-pill py-2"
              elevation="12"
              color="primary"
              x-small
              @click="showTable"
              v-if="this.role !== 'bank' && this.loan.accepted"
              >Amortization table</v-btn
            >
          </v-card-title>
          <v-row class="pa-1 px-6 pb-2">
            <v-col cols="2" class="pa-1 offset-1">
              <p class="text-body-2 mb-1">
                Amount: <br />
                {{ loan.amount }}
              </p>
            </v-col>
            <v-col cols="3" class="pa-1">
              <p class="text-body-2 mb-1">
                Rate: <br />
                {{ loan.fund.rate }}% per year
              </p>
            </v-col>
            <v-col cols="2" class="pa-1">
              <p class="text-body-2 mb-1">
                Type: <br />
                {{ loan.is_fund ? "Fund" : "Loan " }}
              </p>
            </v-col>
            <v-col cols="2" class="pa-1">
              <p class="text-body-2 mb-1">
                Duration: <br />
                {{ loan.fund.duration }} years
              </p>
            </v-col>
            <v-col cols="2" class="pa-1" align="center" justify="center">
              <v-btn
                v-if="this.role === 'bank' && !this.loan.accepted"
                elevation="2"
                outlined
                color="success"
                @click="accept"
                >Accept</v-btn
              >
              <h1
                class="text-body-2"
                v-if="this.role != 'bank' && !this.loan.accepted"
              >
                Pending <br />
                Approval
              </h1>
              <v-btn
                class="text-body-2"
                color="success"
                @click="show"
                v-if="this.role === 'customer' && this.loan.accepted"
              >
                Pay
              </v-btn>
            </v-col>
          </v-row>
        </v-card>
      </v-col>
    </v-row>
    <v-overlay :value="overlay">
      <v-progress-circular indeterminate size="64"></v-progress-circular>
    </v-overlay>
    <v-overlay :value="tableOverlay">
      <table-comp :loan="loan" @close-table="closeTable"></table-comp>
    </v-overlay>
    <v-overlay :value="overlay2" v-if="this.role == 'customer'">
      <v-card dark>
        <v-card-title justify="end">
          <v-btn @click="close" color="red">close</v-btn>
        </v-card-title>
        <v-row justify="center">
          <v-col cols="11">
            <v-text-field
              type="number"
              v-model="amount"
              label="Amount"
              filled
              rounded
              dense
            ></v-text-field>
          </v-col>
        </v-row>
        <v-row justify="center">
          <v-col cols="3">
            <v-btn @click="pay" color="success">Pay</v-btn>
          </v-col>
        </v-row>
      </v-card>
    </v-overlay>
  </v-container>
</template>

<script>
import axios from "axios";
import TableComp from "./TableComp.vue";

export default {
  name: "LoanItem",
  data: () => ({
    role: sessionStorage.getItem("Role"),
    overlay: false,
    alert: false,
    success: false,
    overlay2: false,
    tableOverlay: false,
    amount: undefined,
    message: "",
  }),
  components: { TableComp },
  methods: {
    showTable() {
      this.tableOverlay = true;
    },
    closeTable() {
      this.tableOverlay = false;
    },
    pay() {
      let data = {
        amount: this.amount,
        id: this.loan.id,
      };
      axios
        .post("http://127.0.0.1:8000/app/pay/", data, {
          headers: {
            token: sessionStorage.getItem("Token"),
          },
        })
        .then((response) => {
          this.$emit("refresh");
          this.success = true;
          this.message = response.data["message"];
        })
        .catch((error) => {
          this.alert = true;
          this.message = error.response.data.message;
        });
      this.overlay2 = false;
    },
    show() {
      this.overlay2 = true;
    },
    accept() {
      this.overlay = true;
      let data = {
        id: this.loan.id,
      };
      axios
        .post("http://127.0.0.1:8000/app/accept/", data, {
          headers: {
            token: sessionStorage.getItem("Token"),
          },
        })
        .then((response) => {
          
          this.success = true;
          this.message = response.data["message"];
        })
        .catch((error) => {
          this.alert = true;
          this.message = error.response.data.message;
        });
      setTimeout(() => {
        this.$emit("refresh");
        this.overlay = false;
      }, 300);
    },
    close() {
      this.overlay2 = false;
    },
  },
  props: ["loan"],
  mounted() {
    console.log(this.loan);
  },
};
</script>
