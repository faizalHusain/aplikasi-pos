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
      <v-btn color="blue" @click="createProduct()">Add</v-btn>
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
    <v-data-table :headers="headers" :items="products_display" :search="search">
      <template v-slot:[`item.actions`]="{ item }">
        <v-icon small class="mr-2" @click="editProduct(item.id)"
          >mdi-pencil</v-icon
        >
        <v-icon small class="mr-2" @click="deleteProduct(item.id)"
          >mdi-delete</v-icon
        >
        <v-icon small class="mr-2" @click="infoProduct(item.id)">
          mdi-information-outline
        </v-icon>
      </template>
    </v-data-table>
    <p class="mt-3">{{ message }}</p>
    <template>
      <v-dialog
        v-model="dialog"
        transition="dialog-bottom-transition"
        width="500"
      >
        <v-card>
          <v-card-title class="text-h5 dark lighten-2">
            {{ product_info.product_name }}
          </v-card-title>
          <v-divider class="my-3"></v-divider>
          <v-card-subtitle class="mt-2"> Code </v-card-subtitle>
          <v-card-text>
            {{ product_info.product_code }}
          </v-card-text>
          <v-card-subtitle class="mt-2"> Description </v-card-subtitle>
          <v-card-text>
            {{ product_info.description }}
          </v-card-text>
          <v-card-subtitle class="mt-2"> Stock </v-card-subtitle>
          <v-card-text>
            {{ product_info.uom_stock }}
          </v-card-text>

          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="warning" text @click="editProduct(product_info.id)">
              Edit
            </v-btn>
            <v-btn color="primary" text @click="dialog = false"> OK </v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>
    </template>
  </div>
</template>

<script>
export default {
  layout: "default",
  data() {
    return {
      products_display: [],
      products: [],
      message: "",
      title: "",
      search: "",
      dialog: false,
      headers: [
        {
          text: "Product Code",
          align: "start",
          sortable: false,
          value: "product_code",
        },
        { text: "Product Name", value: "product_name", sortable: true },
        { text: "Stock", value: "stock", sortable: true },
        { text: "Uom", value: "uom_name", sortable: false },
        { text: "Unit Price", value: "unit_price", sortable: true },
        { text: "Description", value: "description", sortable: false },
        { text: "Actions", value: "actions", sortable: false },
      ],
      product_info: {
        product_code: "",
        product_name: "",
        uom_stock: "",
        unit_price: 0,
        description: "",
      },
    };
  },
  methods: {
    infoProduct(id) {
      var product = this.products.filter((obj) => {
        return obj.id === id;
      })[0];
      this.product_info = {
        id: product.id,
        product_code: product.product_code,
        product_name: product.product_name,
        uom_stock: `${product.stock} ${product.uom_name}`,
        unit_price: product.unit_price,
        description: product.description,
      };
      console.log(this.product_info);
      this.dialog = true;
    },
    deleteProduct(id) {
      this.$axios
        .$delete(`products/delete`, {
          data: {
            id: id,
          },
        })
        .then(() => {
          this.message = "Product deleted succesfully";
          this.refreshList();
        })
        .catch((e) => {
          console.log(e);
        });
    },
    editProduct(id) {
      this.$router.push(`product/edit/${id}`);
    },
    createProduct() {
      this.$router.push(`product/create`);
    },
    getDisplayProduct(product) {
      return {
        id: product.id,
        product_code: product.product_code,
        product_name:
          product.product_name.length > 30
            ? product.product_name.substr(0, 30) + "..."
            : product.product_name,
        uom_name:
          product.uom_name.length > 30
            ? product.uom_name.substr(0, 30) + "..."
            : product.uom_name,
        description:
          product.description.length > 30
            ? product.description.substr(0, 30) + "..."
            : product.description,
        unit_price: product.unit_price,
        stock: product.stock,
      };
    },
    retrieveProducts() {
      this.$axios
        .$get("products/list")
        .then((response) => {
          this.products_display = response.data.map(this.getDisplayProduct);
          this.products = response.data;
          console.log(response.data);
        })
        .catch((e) => {
          console.log(e);
        });
    },
    refreshList() {
      this.retrieveProducts();
    },
    // getProduct: async function () {
    //   const response = await this.$axios.$get("products");
    //   console.log(response);
    // },
  },
  mounted() {
    this.retrieveProducts();
    this.message = "";
  },
};
</script>
