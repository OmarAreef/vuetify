<template>
  <v-container class="lighten-5 mb-6" fill-height fill-width>
    <v-row
      align="center"
      justify="center"
      style="height: fit-content; width: fit-content"
    >
      <v-col cols="6">
        <v-card elevation="8" outlined class="pa-1 rounded-lg">
          <v-form v-model="valid">
            <v-row>
              <v-col cols="6">
                <v-text-field
                  v-model="username"
                  label="Username"
                  required
                ></v-text-field>
              </v-col>
            </v-row>
            <v-row>
              <v-col cols="6">
              <v-text-field
                v-model="password"
                label="password"
                required
              ></v-text-field>
              </v-col>
            </v-row>
          </v-form>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
import axios from "axios";

export default {
  name: "Home",
  data: () => ({
    valid: false,
    username: "",
    password: "",
  }),
};
</script>
