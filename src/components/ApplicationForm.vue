<template>
  <v-container style="min-width: 450px; min-height: 450px">
    <v-card dark>
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
        {{ message }}
      </v-alert>
      <v-card-title justify="end">
        <v-btn @click="close" color="red">close</v-btn></v-card-title
      >
      <v-form v-model="valid" lazy-validation ref="form">
        <v-row justify="center">
          <v-col cols="11">
            <v-text-field
              type="number"
              v-model="amount"
              label="Amount"
              filled
              rounded
              dense
              :rules="amountRules"
            ></v-text-field>
          </v-col>
        </v-row>
        <v-row justify="center">
          <v-col cols="11">
            <v-select
              :items="this.funds"
              return-object
              label="Select fund"
              v-model="fundSelected"
              @change="selectFund"
            >
              <template v-slot:item="{ item }">
                <v-row justify="center" class="text-body-2">
                  <v-col>
                    rate:<br />
                    {{ item.rate }}
                  </v-col>
                  <v-col>
                    min amount:<br />
                    {{ item.min_amount }}
                  </v-col>
                  <v-col> max amount:<br />{{ item.max_amount }} </v-col>
                  <v-col> duration:<br />{{ item.duration }} </v-col>
                </v-row>
              </template>
              <template v-slot:selection="{ item }">
                <v-row justify="center" class="text-body-2">
                  <v-col>
                    rate:<br />
                    {{ item.rate }}
                  </v-col>
                  <v-col>
                    min amount:<br />
                    {{ item.min_amount }}
                  </v-col>
                  <v-col> max amount:<br />{{ item.max_amount }} </v-col>
                  <v-col> duration:<br />{{ item.duration }} </v-col>
                </v-row>
              </template>
            </v-select>
          </v-col>
        </v-row>
        <v-row justify="center">
          <v-col cols="3">
            <v-btn class="rounded-pill" color="green" @click="submit"
              >Submit application
            </v-btn>
          </v-col>
        </v-row>
      </v-form>
    </v-card>
  </v-container>
</template>

<script>
import axios from "axios";


export default {
  name: "ApplicationForm",
  data: () => ({
    message: "",
    alert: false,
    amount: "",
    valid: false,
    role: sessionStorage.getItem("Role"),
    token: sessionStorage.getItem("Token"),
    fundSelected: {},
    amountRules: [
      (v) => !!v || "field is required",
      (v) =>
        (v &&
          sessionStorage.getItem("min") &&
          sessionStorage.getItem("max") &&
          v >= parseInt(sessionStorage.getItem("min")) &&
          v <= parseInt(sessionStorage.getItem("max"))) ||
        "You have to select a loan or the amount entered is not within the range of min and max",
    ],
  }),
  methods: {
    close() {
      this.$emit("close-overlay");
    },
    selectFund() {
      sessionStorage.setItem("min", this.fundSelected.min_amount);
      sessionStorage.setItem("max", this.fundSelected.max_amount);
    },
    submit() {
      if (this.$refs.form.validate()) {
        let data = {
          amount: parseInt(this.amount),
          fund: this.fundSelected,
        };
        console.log(data);
        axios
          .post("http://127.0.0.1:8000/app/loan/", data, {
            headers: { token: sessionStorage.getItem("Token") },
          })
          .then((response) => {
            this.$emit("close-overlay");
          })
          .catch((error) => {
              this.message = error.response;
              this.alert = true;
              
          });
      }
    },
  },
  props: ["funds"],
};
</script>
