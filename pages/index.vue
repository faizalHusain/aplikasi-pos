<template>
  <div>
    <v-container>
      <v-row no-gutters>
        <v-col
          v-for="[icon, title, value] in dashboards"
          :key="icon"
          cols="12"
          sm="6"
        >
          <v-card class="py-5 mx-2" style="height: 200px">
            <div class="d-flex align-center">
              <v-icon size="150px">{{ icon }}</v-icon>
              <div class="d-flex flex-column">
                <h3>{{ title }}</h3>
                <h1 style="font-size: 50pt">{{ value }}</h1>
              </div>
            </div>
          </v-card>
        </v-col>
      </v-row>
    </v-container>
  </div>
</template>

<script>
export default {
  layout: "default",
  data() {
    return {
      dashboards: [
        ["mdi-account-multiple", "Total Customer"],
        ["mdi-currency-usd", "Total Omset"],
      ],
    };
  },
  methods: {
    getProduct: async function () {
      const response = await this.$axios.$get("dashboard", {
        withCredentials: true,
      });
      this.dashboards[0].push(response.data.total_customer);
      this.dashboards[1].push(response.data.total_omset);
      console.log(response);
    },
  },
  mounted() {
    this.getProduct();
  },
};
</script>
