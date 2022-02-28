<template>
  <v-app>
    <v-main>
      <router-view />
    </v-main>
  </v-app>
</template>

<style scoped>
.v-application {
  background-color: #232768ef !important
}

</style>
<script>
export default {
  name: "App",

  data: () => ({
    //
  }),
};
</script>
