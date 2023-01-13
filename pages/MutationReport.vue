<template>
  <div>
    <!-- <v-container>
        <v-row no-gutters>
          <v-col v-for="n in 3" :key="n" cols="12" sm="4">
            <v-card class="pa-2 mx-2"> One of three columns </v-card>
          </v-col>
        </v-row>
      </v-container> -->
    <div class="d-flex justify-space-between">
        <v-card-title>
          <v-text-field
            v-model="search"
            append-icon="mdi-magnify"
            label="Search"
            single-line
            hide-details
            outlined
            dense
          ></v-text-field>
        </v-card-title>
    </div>
    <v-row >
      <v-col
        cols="4"
        sm="6"
        md="6"
      >
        <v-text-field
          v-model="dateRangeText"
          label="Date range"
          prepend-icon="mdi-calendar"
          readonly
        ></v-text-field>
      </v-col>
      <v-col
        cols="8"
        sm="6"
        md="6">
        <v-data-table
          :headers="headers"
          :items="mutations_display"
          :search="search"
        >
        </v-data-table>
      </v-col>
    </v-row>
    <v-row no-gutters>
      <v-col
        cols="8"
        sm="6"
      >
        <v-date-picker
          v-model="dates"
          range
        ></v-date-picker>
      </v-col>
      <v-col>
        <v-btn color="primary" class="mt-3" @click="submitDate">Submit</v-btn>
      </v-col>
    </v-row>
    <p class="mt-3">{{ message }}</p>
  </div>
</template>

<script>
export default {
  layout: "default",
  data() {
    return {
      mutations_display: [],
      mutations: [],
      message: "",
      title: "",
      search: "",
      dates: [(new Date(Date.now() - (new Date()).getTimezoneOffset() * 60000)).toISOString().substr(0, 10), 
      (new Date(Date.now() - (new Date()).getTimezoneOffset() * 60000)).toISOString().substr(0, 10)],
      
      headers: [
        { text: "Product Name", align: "start", sortable: true, value: "product_name", },
        { text: "Total Adjust", value: "total_adjust", sortable: true, },
        { text: "Total Buy", value: "total_buy", sortable: true },
        { text: "Total Sell", value: "total_sell", sortable: false, },
        { text: "Initial Stock", value: "initial_stock", sortable: true },
        { text: "Final Stock", value: "final_stock", sortable: false, },
      ],
    };
  },
  computed: {
    dateRangeText () {
      return this.dates.join(' ~ ')
    },
  },
  methods: {
    submitDate(){
      var data = {
        start_date: this.dates[0],
        end_date: this.dates[1],
      };
      this.$axios.$post(`mutationreport`, data)
        .then((response) => {
          this.mutations = response.data;
          console.log(response.data);
        })
        .catch((e) => {
          console.log(e);
        });
    },
    getDisplayMutation(mutation) {
      return {
        product_id: mutation.product_id,
        product_name: mutation.product_name.length > 30 ? mutation.product_name.substr(0, 30) + "..." : mutation.product_name,
        total_adjust: mutation.total_adjust,
        total_buy: mutation.total_buy,
        total_sell: mutation.total_sell,
        initial_stock: mutation.initial_stock,
        final_stock: mutation.final_stock,
      };
    },
    retrieveMutations() {
      this.$axios.$post("mutationreport")
        .then((response) => {
          this.mutations_display = response.data.map(this.getDisplayMutation);
          this.mutations = response.data
          console.log(response.data);
        })
        .catch((e) => {
          console.log(e);
        });
    },
    refreshList(){
      this.retrieveMutations();
    }
    // getMutation: async function () {
    //   const response = await this.$axios.$get("mutations");
    //   console.log(response);
    // },
  },
  mounted() {
    this.retrieveMutations();
    this.message="";
  },
};
</script>
