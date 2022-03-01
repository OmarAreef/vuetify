<template>
  <v-container class="mb-6">
    <v-row align="center" justify="center" class="mt-10">
      <img src="../assets/blnk.png" alt="" />
    </v-row>
    <v-row align="center" justify="center" class="mt-10">
      <v-col cols="6">
        <v-alert
          v-if="alert"
          color="red"
          v-model="alert"
          dismissible
          elevation="8"
          shaped
          type="error"
          >username or password is incorrect please try again or Register a new
          account</v-alert
        >
      </v-col>
    </v-row>
    <v-row align="center" justify="center" class="text-center mt-10">
      <v-col cols="6">
        <v-card
          elevation="10"
          outlined
          class="pa-3 rounded-lg mt-n10"
          justify="center"
          :loading="loading"
          
        >
          <template slot="progress">
            <v-progress-linear
              color="success"
              height="5"
              indeterminate
            ></v-progress-linear>
          </template>
          <v-card-title>
            <v-row>
              <v-col cols="6" offset="3">
                <h2 class="display-1 text-center">Login</h2>
              </v-col>
            </v-row>
          </v-card-title>
          <v-form v-model="valid" lazy-validation ref="form">
            <v-row justify="center">
              <v-col cols="9">
                <v-text-field
                  v-model="username"
                  label="Username"
                  :rules="nameRules"
                  filled
                  rounded
                  dense
                ></v-text-field>
              </v-col>
            </v-row>
            <v-row justify="center">
              <v-col cols="9">
                <v-text-field
                  type="password"
                  v-model="password"
                  label="password"
                  :rules="nameRules"
                  filled
                  rounded
                  dense
                ></v-text-field>
              </v-col>
            </v-row>
            <v-row justify="center">
              <v-col cols="4">
                <v-btn
                  elevation="6"
                  outlined
                  rounded
                  text
                  color="success"
                  @click="login"
                >
                  login
                </v-btn>
              </v-col>
              <v-col cols="4">
                <v-btn
                  elevation="6"
                  outlined
                  rounded
                  text
                  color="secondary"
                  @click="register"
                >
                  Register
                </v-btn>
              </v-col>
            </v-row>
          </v-form>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<style scoped></style>

<script>
import axios from "axios";
import router from "../router";

export default {
  name: "Home",
  data: () => ({
    valid: false,
    username: "",
    password: "",
    nameRules: [
      (v) => !!v || "field is required",
      (v) => (v && v.length >= 3) || "field must be greater than 3 characters",
    ],
    loading: false,
    alert: false,
  }),
  methods: {
    login() {
      if (this.$refs.form.validate()) {
        this.loading = true;
        let data = {
          username: this.username,
          password: this.password,
        };
        axios
          .post("http://127.0.0.1:8000/app/login/", data)
          .then((response) => {
            let myCookie = document.cookie + "; jwt=" + response.data["token"];
            sessionStorage.setItem("Token", response.data["token"]);
            sessionStorage.setItem("Role", response.data["role"]);
            router.push("dashboard");
          })
          .catch((error) => {
            console.log(error);
            setTimeout(() => {
              this.loading = false;
              this.alert = true;
            }, 250);
          });
      }
    },
    register() {
      router.push("register");
    },
  },
};
</script>
