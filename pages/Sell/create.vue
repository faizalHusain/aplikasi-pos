<template>
  <div>
    <v-container class="d-flex flex-row">
      <v-row no-gutters>
        <v-col cols="12" sm="8">
          <v-card class="pa-2 mx-2" style="height: 80vh">
            <v-btn
              color="primary"
              dark
              @click.stop="dialog = true"
              style="width: 100%"
              class="mb-7"
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
                      ref="products"
                      v-model="product"
                      :items="products"
                      dense
                      outlined
                      filled
                      label="Product"
                      item-text="product_name"
                      required
                      return-object
                    ></v-autocomplete>
                    <v-text-field
                      outlined
                      label="quantity"
                      dense
                      v-model="quantity"
                    >
                    </v-text-field>
                  </div>
                </v-card-text>
                <v-divider></v-divider>
                <v-card-actions>
                  <v-spacer></v-spacer>
                  <v-btn color="blue" text @click="addOrder"> Simpan </v-btn>
                </v-card-actions>
              </v-card>
            </v-dialog>
            <v-simple-table>
              <template v-slot:default>
                <thead>
                  <tr>
                    <th class="text-left">Produk</th>
                    <th class="text-left">Price</th>
                    <th class="text-left">Quantity</th>
                  </tr>
                </thead>
                <tbody>
                  <tr v-for="item in orders" :key="item.name">
                    <td>{{ item.product_name }}</td>
                    <td>
                      {{ item.unit_price }}
                    </td>
                    <td>
                      <v-text-field
                        style="width: 50px"
                        dense
                        v-model="item.quantity"
                      >
                      </v-text-field>
                    </td>
                  </tr>
                </tbody>
              </template>
            </v-simple-table>
          </v-card>
        </v-col>
        <v-col cols="12" sm="4">
          <v-card class="pa-2 mx-2" height="80vh">
            <v-autocomplete
              v-model="query"
              :items="items"
              dense
              filled
              outlined
              label="Customer"
              class="mb-0"
            ></v-autocomplete>

            <v-simple-table class="mt-0">
              <template v-slot:default>
                <thead>
                  <tr>
                    <th class="text-left">Produk</th>
                    <th class="text-left">Quantity</th>
                    <th class="text-left">Total</th>
                  </tr>
                </thead>
                <tbody>
                  <tr v-for="item in orders" :key="item.id">
                    <td>{{ item.product_name }}</td>
                    <td>{{ item.quantity }}</td>
                    <td>{{ item.unit_price * item.quantity }}</td>
                  </tr>
                </tbody>
              </template>
            </v-simple-table>

            <v-card-actions style="position: absolute; bottom: 0; width: 100%">
              <div class="d-flex flex-column my-2" style="width: 100%">
                <v-btn color="red" style="width: 95%; margin-bottom: 5px"
                  >Cancel</v-btn
                >
                <v-btn color="blue" style="width: 95%">Rp 50.000</v-btn>
              </div>
            </v-card-actions>
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
      product: {},
      quantity: "",
      orders: [],
      products: [],
    };
  },
  methods: {
    getProduct: async function () {
      const response = await this.$axios.$get("products");
      console.log(response.data);
      this.products = response.data;
    },
    addOrder: function () {
      this.dialog = false;
      this.product.quantity = this.quantity;
      console.log(this.product);
      this.orders.push(this.product);
    },
  },
  mounted() {
    this.getProduct();
  },
};
</script>
