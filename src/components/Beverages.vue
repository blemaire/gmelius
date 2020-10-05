<template>
  <v-container>
    <v-row class="text-center">
      <v-col cols="12">
        <h1 class="display-2 font-weight-bold mb-3">
          My Beers
        </h1>
      </v-col>
    </v-row>

    <v-row class="text-center">
      <v-col cols="12">
        <v-data-table
          :loading="loading"
          :headers="headers"
          :items="beers"
          :items-per-page="5"
          class="elevation-1"
          hide-default-footer
          @page-count="pageCount = $event"
        ></v-data-table>
      </v-col>
    </v-row>

    <v-row class="text-center">
      <v-col cols="12">
        <v-pagination v-model="page" :length="6"></v-pagination>
      </v-col>
    </v-row>
  </v-container>
</template>

<script lang="ts">
import Vue from "vue";

export default Vue.extend({
  name: "Beverages",

  data: () => ({
    beers: [],
    headers: [
      {
        text: "Name",
        align: "start",
        sortable: false,
        value: "name"
      },
      { text: "Description", value: "description" }
    ],
    loading: true,
    page: 1,
    pageCount: 0
  }),

  created(): void {
    this.getBeverages(1);
  },

  methods: {
    getBeverages(page: number) {
      this.loading = true;

      fetch(`https://api.punkapi.com/v2/beers?page=${page || 0}`)
        .then(res => res.json())
        .then(response => {
          this.beers = response;
          this.loading = false;
        });
    }
  },

  watch: {
    page(newPageNumber: number) {
      this.getBeverages(newPageNumber);
    }
  },
});
</script>
