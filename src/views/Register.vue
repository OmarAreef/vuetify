<template>
  <v-container class="mb-6">
    <v-row align="center" justify="center" class="mt-10">
      <img src="../assets/blnk.png" alt="" />
    </v-row>
    <v-row align="center" justify="center" class="mt-5">
      <v-alert
        v-if="alert"
        color="red"
        v-model="alert"
        dismissible
        elevation="8"
        shaped
        type="error"
        >username is already in use, please try again with a different username</v-alert
      >
    </v-row>
    <v-row align="center" justify="center" class="text-center mt-5">
      <v-col cols="6">
        <v-card
          elevation="10"
          outlined
          class="pa-3 rounded-lg"
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
                <h2 class="text-h2 text-center">Register</h2>
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
                  label="Password"
                  :rules="nameRules"
                  filled
                  rounded
                  dense
                ></v-text-field>
              </v-col>
            </v-row>
            <v-row justify="center">
              <v-col cols="9">
                <v-select
                  v-model="role"
                  label="Role"
                  :items="items"
                  :rules="nameRules"
                  filled
                  rounded
                  dense
                ></v-select>
              </v-col>
            </v-row>
            <v-row justify="center">
              <v-col cols="6">
                <v-btn
                  elevation="6"
                  outlined
                  rounded
                  text
                  color="success"
                  x-large
                  @click="Register"
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
<style scoped>
.col-9 {
  max-height: 70px !important;
}
</style>

<script>
import axios from "axios";
import router from "../router";

export default {
  name: "Register",
  data: () => ({
    valid: false,
    items: ["customer", "provider"],
    username: "",
    password: "",
    role: "",
    nameRules: [
      (v) => !!v || "field is required",
      (v) => (v && v.length >= 3) || "field must be greater than 3 characters",
    ],
    loading: false,
    alert: false,
  }),
  methods: {
    Register() {
      if (this.$refs.form.validate()) {
        this.loading = true;
        let data = {
          username: this.username,
          password: this.password,
          role: this.role,
        };
        axios
          .post("http://127.0.0.1:8000/app/register/", data)
          .then((response) => {
            let myCookie = document.cookie + "; jwt=" + response.data["token"];
            document.cookie = myCookie;
            router.push("/");
          })
          .catch((error) => {
            console.log(error.response.data.message);
            setTimeout(() => {
              this.loading = false;
              this.alert = true;
            }, 250);
          });
      }
    },
  },
};
</script>
