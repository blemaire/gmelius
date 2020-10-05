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
          :server-items-length="total"
          :loading="loading"
          :headers="headers"
          :items="beers"
          class="elevation-1"
          :options.sync="options"
        ></v-data-table>
      </v-col>
    </v-row>
  </v-container>
</template>

<script lang="ts">
import Vue from "vue";
import { DataOptions } from "vuetify";

export interface FetchParams extends Partial<DataOptions> {
  page: number;
  itemsPerPage: number;
}

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
      {
        text: "Description",
        value: "description",
        align: "start",
        sortable: false
      },
      { text: "ABV", value: "abv", align: "start", sortable: false }
    ],
    loading: true,
    options: { itemsPerPage: 10, page: 1 } as DataOptions,
    strongOnly: false,
    total: 0,
    lastPage: 0
  }),

  methods: {
    fetchBeverages(options: FetchParams) {
      // const isNewPage = options.page * options.itemsPerPage > this.beers.length;
      //
      // if (!isNewPage) {
      //   return;
      // }

      const stringParams = this.buildSearchParams({ ...options });

      this.loading = true;

      fetch(
        `https://api.punkapi.com/v2/beers${
          stringParams ? "?" + stringParams : ""
        }`
      )
        .then(res => res.json())
        .then(response => {
          if (response.length === 0) {
            this.lastPage = options.page - 1;
            this.options.page = this.lastPage;
            return;
          }

          let newTotal =
            (options.page - 1) * options.itemsPerPage + response.length;

          if (response.length === options.itemsPerPage) {
            // if the current page is full add at least one to the total to enable the 'next page' button.
            newTotal++;
          }

          if (newTotal > this.total) {
            this.total = newTotal;
          }

          this.beers = response;
          this.loading = false;
        });
    },

    buildSearchParams(options: DataOptions | Partial<DataOptions>) {
      return Object.entries({
        page: options.page || 1,
        // eslint-disable-next-line @typescript-eslint/camelcase
        abv_gt: this.strongOnly ? 7.5 : 0,
        // eslint-disable-next-line @typescript-eslint/camelcase
        per_page: options.itemsPerPage
      })
        .map(([key, value]) => {
          return `${key}=${value}`;
        })
        .join("&");
    }
  },

  watch: {
    strongOnly() {
      this.fetchBeverages(this.options);
    },
    options: {
      deep: true,
      handler(options: DataOptions) {
        this.fetchBeverages(options);
      }
    }
  }
});
</script>
