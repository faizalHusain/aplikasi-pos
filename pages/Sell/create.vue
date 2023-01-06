<template>
  <div>
    <v-container class="d-flex flex-row">
      <v-row no-gutters>
        <v-col cols="12" sm="8">
          <v-card class="pa-2 mx-2">
            <v-btn
              color="primary"
              dark
              @click.stop="dialog = true"
              style="width: 100%"
            >
              Tambah
            </v-btn>

            <v-dialog v-model="dialog" max-width="500">
              <v-card>
                <v-card-title class="text-h5 blue">
                  Tambah Produk
                </v-card-title>

                <v-card-text>
                  <div class="d-flex flex-column justify-space-around my-4">
                    <v-autocomplete
                      v-model="value"
                      :items="items"
                      dense
                      outlined
                      filled
                      label="Product"
                    ></v-autocomplete>
                    <v-text-field outlined label="quantity" dense>
                    </v-text-field>
                  </div>
                </v-card-text>
                <v-divider></v-divider>
                <v-card-actions>
                  <v-spacer></v-spacer>
                  <v-btn color="blue" text @click="dialog = false">
                    Simpan
                  </v-btn>
                </v-card-actions>
              </v-card>
            </v-dialog>
            <v-simple-table>
              <template v-slot:default>
                <thead>
                  <tr>
                    <th class="text-left">Name</th>
                    <th class="text-left">Calories</th>
                  </tr>
                </thead>
                <tbody>
                  <tr v-for="item in desserts" :key="item.name">
                    <td>{{ item.name }}</td>
                    <td>{{ item.calories }}</td>
                  </tr>
                </tbody>
              </template>
            </v-simple-table>
          </v-card>
        </v-col>
        <v-col cols="12" sm="4">
          <v-card class="pa-2 mx-2">
            <v-autocomplete
              v-model="value"
              :items="items"
              dense
              filled
              label="Customer"
            ></v-autocomplete>
            <v-simple-table>
              <template v-slot:default>
                <thead>
                  <tr>
                    <th class="text-left">Name</th>
                    <th class="text-left">Calories</th>
                  </tr>
                </thead>
                <tbody>
                  <tr v-for="item in desserts" :key="item.name">
                    <td>{{ item.name }}</td>
                    <td>{{ item.calories }}</td>
                  </tr>
                </tbody>
              </template>
            </v-simple-table>
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
      dialog: false,
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
