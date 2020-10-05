<template>
  <v-container>
    <v-row class="text-center">
      <v-col cols="12">
        <h1 class="display-2 font-weight-bold mb-3">
          My Beers
        </h1>
      </v-col>
    </v-row>

    <v-row>
      <v-col class="text-lg-right">
        <v-switch v-model="strongOnly" label="Strong beers only"></v-switch>
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
    pageCount: 0,
    strongOnly: false
  }),

  created(): void {
    this.getBeverages(this.buildSearchParams());
  },

  methods: {
    getBeverages(params: string) {
      this.loading = true;

      fetch(`https://api.punkapi.com/v2/beers?${params}`)
        .then(res => res.json())
        .then(response => {
          this.beers = response;
          this.loading = false;
        });
    },

    buildSearchParams() {
      return Object.entries({
        page: this.page || 1,
        // eslint-disable-next-line @typescript-eslint/camelcase
        abv_gt: this.strongOnly ? 7.5 : 0
      })
        .map(([key, value]) => {
          return `${key}=${value}`;
        })
        .join("&");
    }
  },

  watch: {
    page() {
      this.getBeverages(this.buildSearchParams());
    },
    strongOnly() {
      this.getBeverages(this.buildSearchParams());
    }
  }
});
</script>
