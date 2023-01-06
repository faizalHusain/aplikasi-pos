<template>
  <div>
    <v-form>
      <v-text-field label="Email" hide-details="auto"></v-text-field>
      <v-text-field label="Password" type="password"></v-text-field>

      <v-btn color="blue" class="py-5" style="width: auto">Tambah</v-btn>
    </v-form>
  </div>
</template>

<script>
export default {
  layout: "default",
  data() {
    return {
      desserts: [
        {
          name: "Frozen Yogurt",
          calories: 159,
        },
        {
          name: "Ice cream sandwich",
          calories: 237,
        },
        {
          name: "Eclair",
          calories: 262,
        },
        {
          name: "Cupcake",
          calories: 305,
        },
        {
          name: "Gingerbread",
          calories: 356,
        },
        {
          name: "Jelly bean",
          calories: 375,
        },
        {
          name: "Lollipop",
          calories: 392,
        },
        {
          name: "Honeycomb",
          calories: 408,
        },
        {
          name: "Donut",
          calories: 452,
        },
        {
          name: "KitKat",
          calories: 518,
        },
      ],
    };
  },
  methods: {
    getProduct: async function () {
      const response = await this.$axios.$get("products");
      console.log(response);
    },
  },
  mounted() {
    this.getProduct();
  },
};
</script>
