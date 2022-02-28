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
        <v-btn @click="close" color="red">close</v-btn>
      </v-card-title>
      <v-form v-model="valid" lazy-validation ref="form">
        <v-row justify="center">
          <v-col cols="5">
            <v-text-field
              type="number"
              v-model="minAmount"
              label="minimum amount"
              filled
              rounded
              :rules="amountRules"
              dense
            ></v-text-field>
          </v-col>
          <v-col cols="5">
            <v-text-field
              type="number"
              v-model="maxAmount"
              label="max amount"
              filled
              rounded
              :rules="amountRules"
              dense
            ></v-text-field>
          </v-col>
        </v-row>
        <v-row justify="center">
          <v-col cols="4">
            <v-text-field
              type="number"
              v-model="duration"
              label="Duration in years"
              filled
              rounded
              :rules="amountRules"
              dense
            ></v-text-field>
          </v-col>
          <v-col cols="4">
            <v-text-field
              type="number"
              v-model="rate"
              label="rate "
              filled
              :rules="amountRules"
              rounded
              dense
            ></v-text-field>
          </v-col>
          <v-col cols="3">
            <v-checkbox v-model="checkbox" label="is it a Fund"></v-checkbox>
          </v-col>
        </v-row>

        <v-row justify="end " class="pr-10 mr-10">
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
import { v4 as uuidv4 } from "uuid";
export default {
  name: "ApplicationForm",
  data: () => ({
    minAmount: "",
    maxAmount: "",
    rate: "",
    duration: "",
    checkbox: false,
    message: "",
    alert: false,
    valid: false,
    amountRules: [
      (v) => !!v || "field is required",
      (v) => (v && v > 0) || "Number must be greater that zero",
    ],
  }),

  methods: {
    close() {
      this.$emit("close-overlay");
    },
    submit() {
      if (this.$refs.form.validate()) {
        let data = {
          id: uuidv4(),
          min_amount: parseInt(this.minAmount),
          max_amount: parseInt(this.maxAmount),
          duration: parseInt(this.duration),
          rate: parseFloat(parseFloat(this.rate).toFixed(1)),
          is_fund: this.checkbox,
        };
        console.log(data);
        axios
          .post("http://127.0.0.1:8000/app/fund/", data, {
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
};
</script>
