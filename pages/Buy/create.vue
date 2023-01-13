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
                  <tr
                    v-if="renderComponent"
                    v-for="item in orders"
                    :key="item.product_id"
                  >
                    <td>{{ item.product_name }}</td>
                    <td>
                      {{ item.unit_price }}
                    </td>
                    <td>
                      <v-icon @click="decQty(item.product_id)"
                        >mdi-minus-circle</v-icon
                      >
                      {{ item.quantity }}
                      <!-- {{ item.quantity }} -->
                      <v-icon @click="incQty(item.product_id)"
                        >mdi-plus-circle</v-icon
                      >
                    </td>
                  </tr>
                </tbody>
              </template>
            </v-simple-table>
          </v-card>
          <div>
            <h1>{{ orders }}</h1>
          </div>
        </v-col>
        <v-col cols="12" sm="4">
          <v-card v-if="renderComponent" class="pa-2 mx-2" height="80vh">
            <v-autocomplete
              v-model="customer"
              :items="customers"
              dense
              filled
              outlined
              label="Supplier"
              class="mb-0"
              item-text="name"
              required
              return-object
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
                  <tr v-for="item in orders" :key="item.product_id">
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
                <v-btn
                  @click="createSellOrders"
                  color="blue"
                  style="width: 95%"
                  >{{ sum(orders) }}</v-btn
                >
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
      renderComponent: true,
      dialog: false,
      product: {},
      quantity: "",
      orders: [],
      products: [],
      customers: [],
      customer: {},
    };
  },
  methods: {
    getProduct: async function () {
      const response = await this.$axios.$get("products/list");
      console.log(response.data);
      this.products = response.data;
    },
    addOrder: function () {
      this.dialog = false;
      console.log(this.product);
      const order = {
        product_id: this.product.id,
        unit_price: this.product.unit_price,
        product_name: this.product.product_name,
        quantity: parseInt(this.quantity),
      };
      this.orders.push(order);
    },
    getCustomer: async function () {
      const response = await this.$axios.$get("suppliers/list");
      console.log(response.data);
      this.customers = response.data;
    },
    incQty: async function (id) {
      const index = this.orders.findIndex((obj) => obj.product_id == id);
      // Vue.set(orders, [index].quantity, [index].quantity - 1);
      // this.$set(this.orders,[index].quantity, 6);
      this.orders[index].quantity = parseInt(this.orders[index].quantity) + 1;
      this.renderComponent = false;

      await this.$nextTick();
      this.renderComponent = true;
      console.log(this.orders);
    },
    decQty: async function (id) {
      const index = this.orders.findIndex((obj) => obj.product_id == id);
      // Vue.set(orders, [index].quantity, [index].quantity - 1);
      // this.$set(this.orders,[index].quantity, 6);
      this.renderComponent = false;
      this.orders[index].quantity = parseInt(this.orders[index].quantity) - 1;
      await this.$nextTick();
      this.renderComponent = true;
      console.log(this.orders);
    },
    sum: function (orders_bucket) {
      let total = 0;
      orders_bucket.forEach((order) => {
        total += order.quantity * order.unit_price;
      });
      return total;
    },
    createSellOrders: async function () {
      try {
        const sellOrderRes = await this.$axios.$post("/buy_orders/create", {
          supplier_id: this.customer.id,
        });
        console.log(sellOrderRes.data);
        this.orders.forEach((order) => {
          order.order_id = sellOrderRes.data.id;
          this.$axios
            .$post("/buys/create", order)
            .then((res) => {
              console.log(res);
            })
            .catch((e) => {
              console.log(e);
            });
        });
        this.$router.push("/adjust");
      } catch (error) {}
    },
  },
  mounted() {
    this.getProduct();
    this.getCustomer();
    // this.decQty();
    // this.incQty();
  },
};
</script>
