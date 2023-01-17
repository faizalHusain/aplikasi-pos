<template>
  <div>
    <!-- <v-container>
        <v-row no-gutters>
          <v-col v-for="n in 3" :key="n" cols="12" sm="4">
            <v-card class="pa-2 mx-2"> One of three columns </v-card>
          </v-col>
        </v-row>
      </v-container> -->
    <div class="d-flex flex-row justify-space-between">
      <div>
        <v-menu
          ref="menu"
          v-model="menu"
          :close-on-content-click="false"
          :return-value.sync="dates"
          transition="scale-transition"
          offset-y
          min-width="auto"
        >
          <template v-slot:activator="{ on, attrs }" style="width: 20%">
            <v-combobox
              v-model="dates"
              multiple
              chips
              small-chips
              label="Range"
              prepend-icon="mdi-calendar"
              readonly
              v-bind="attrs"
              v-on="on"
              width="auto"
              dense
              outlined
            ></v-combobox>
          </template>
          <v-date-picker v-model="dates" range>
            <v-spacer></v-spacer>
            <v-btn text color="primary" @click="menu = false"> Cancel </v-btn>
            <v-btn text color="primary" @click="submitDate"> OK </v-btn>
          </v-date-picker>
        </v-menu>
      </div>
      <div style="width: 30%">
        <v-text-field
          v-model="search"
          append-icon="mdi-magnify"
          label="Search"
          single-line
          hide-details
          outlined
          dense
          width="30%"
        ></v-text-field>
      </div>
    </div>

    <!-- <v-text-field
      v-model="dateRangeText"
      label="Date range"
      prepend-icon="mdi-calendar"
      readonly
    ></v-text-field> -->

    <!-- <v-btn color="primary" class="mt-3" >Submit</v-btn> -->
    <v-data-table :headers="headers" :items="mutations" :search="search">
    </v-data-table>
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
      dates: [],
      menu: false,
      headers: [
        {
          text: "Product Name",
          align: "start",
          sortable: true,
          value: "product_name",
        },
        { text: "Total order", value: "total_order", sortable: true },
        { text: "Total buy", value: "total_profit", sortable: true },
      ],
    };
  },
  computed: {
    dateRangeText() {
      return this.dates.join(" ~ ");
    },
  },
  methods: {
    submitDate() {
      this.$refs.menu.save(this.dates);
      var data = {
        start_date: this.dates[0],
        end_date: this.dates[1],
      };
      this.$axios
        .$post(`buyreport`, data)
        .then((response) => {
          this.mutations = response.data;
          console.log(response.data);
        })
        .catch((e) => {
          console.log(e);
        });
    },
    // getDisplayMutation(mutation) {
    //   return {
    //     product_id: mutation.product_id,
    //     product_name:
    //       mutation.product_name.length > 30
    //         ? mutation.product_name.substr(0, 30) + "..."
    //         : mutation.product_name,
    //     total_adjust: mutation.total_order,
    //     total_buy: mutation.total_profit,
    //   };
    // },
    // retrieveMutations() {
    //   console.log(this.dates);
    //   this.$axios
    //     .$post("sellreport")
    //     .then((response) => {
    //       this.mutations_display = response.data.map(this.getDisplayMutation);
    //       this.mutations = response.data;
    //       console.log(response.data);
    //     })
    //     .catch((e) => {
    //       console.log(e);
    //     });
    // },
    // refreshList() {
    //   this.retrieveMutations();
    // },
    // getMutation: async function () {
    //   const response = await this.$axios.$get("mutations");
    //   console.log(response);
    // },
  },
  mounted() {
    // this.retrieveMutations();
    this.message = "";
  },
};
</script>
