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
    <v-data-table
      :headers="headers"
      :items="products"
      :search="search"
    >
      <template v-slot:[`item.actions`]="{ item }">
        <v-icon small class="mr-2" @click="editProduct(item.id)">mdi-pencil</v-icon>
        <v-icon small @click="deleteProduct(item.id)">mdi-delete</v-icon>
      </template>
    </v-data-table>
    <p class="mt-3">{{ message }}</p>
  </div>
</template>

<script>
export default {
  layout: "default",
  data() {
    return {
      products: [],
      message: "",
      title: "",
      search: "",
      headers: [
        { text: "Product Code", align: "start", sortable: false, value: "product_code", },
        { text: "Product Name", value: "product_name", sortable: true, },
        { text: "Uom", value: "uom_name", sortable: false, },
        { text: "Description", value: "description", sortable: false, },
        { text: "Stock", value: "stock", sortable: true },
        { text: "Unit Price", value: "unit_price", sortable: true },
        { text: "Actions", value: "actions", sortable: false },
      ],
    };
  },
  methods: {
    deleteProduct(id) {
      this.$axios.$delete(`products/delete`, {data: {
        "id": id,
      }})
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
        product_name: product.product_name.length > 30 ? product.product_name.substr(0, 30) + "..." : product.product_name,
        uom_name: product.uom_name.length > 30 ? product.uom_name.substr(0, 30) + "..." : product.uom_name,
        description: product.description.length > 30 ? product.description.substr(0, 30) + "..." : product.description,
        unit_price: product.unit_price,
        stock: product.stock,
      };
    },
    retrieveProducts() {
      this.$axios.$get("products")
        .then((response) => {
          this.products = response.data.map(this.getDisplayProduct);
          console.log(response.data);
        })
        .catch((e) => {
          console.log(e);
        });
    },
    refreshList(){
      this.retrieveProducts();
    }
    // getProduct: async function () {
    //   const response = await this.$axios.$get("products");
    //   console.log(response);
    // },
  },
  mounted() {
    this.retrieveProducts();
    this.message="";
  },
};
</script>
