<template>
  <v-app>
    <v-main>
      <Beverages />
    </v-main>
  </v-app>
</template>

<script lang="ts">
import Vue from "vue";
import Beverages from "./components/Beverages.vue";

export default Vue.extend({
  name: "App",

  components: {
    Beverages
  },

  data: () => ({
    //
  })
});
</script>
