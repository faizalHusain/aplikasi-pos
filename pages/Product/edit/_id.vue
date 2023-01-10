<template>
  <div v-if="currentProduct" class="edit-form py-3">
    <p class="headline">Edit Buy Order</p>

    <v-form ref="form" lazy-validation>
      <v-text-field
        v-model="currentProduct.product_code"
        :rules="[(v) => !!v || 'Product Code is Required']"
        label="Product Code"
        required
      ></v-text-field>

      <v-text-field
        v-model="currentProduct.product_name"
        :rules="[(v) => !!v || 'Product Name is Required']"
        label="Product Name"
        required
      ></v-text-field>

      <div v-if="currentUom" class="edit-form py-3" > Uom Id <br>
        <select v-model="currentUomId" > 
          <option v-for="(item, index) in uomOptions" :value="item.id" :key="index">{{ item.uom_name }}</option>
        </select>
      </div>
      <div v-else>
        <p>uom unavailable</p>
      </div>
      <v-text-field
        v-model="currentProduct.description"
        :rules="[(v) => !!v || 'Description is required']"
        label="Description"
        required
      ></v-text-field>

      <v-text-field
        v-model="currentProduct.unit_price"
        :rules="[(v) => !!v || 'Unit Price is Required']"
        label="Unit Price"
        required
      ></v-text-field>


      <v-divider class="my-5"></v-divider>

      <v-btn color="error" small class="mr-2" @click="deleteProduct">
        Delete
      </v-btn>

      <v-btn color="success" small @click="updateProduct">
        Update
      </v-btn>
    </v-form>

    <p class="mt-3">{{ message }}</p>
  </div>

  <div v-else>
    <p>Product not found</p>
  </div>
</template>

<script>
export default {
  layout: "default",
  data() {
    return {
      currentProduct: null,
      uomOptions: [],
      currentUom: null,
      currentUomId: null,
      message: "",
    };
  },
  methods: {
    deleteProduct() {
      this.$axios.$delete(`products/delete`, {data: {
        "id": this.currentProduct.id,
      }})
        .then((response) => {
          console.log(response.data);
          this.$router.push("/product");
        })
        .catch((e) => {
          console.log(e);
        });
    },
    updateProduct() {
      this.currentProduct.uom_id = this.currentUomId;
      this.$axios.$put(`products/update`, {
        id: this.currentProduct.id,
        product_code: this.currentProduct.product_code,
        product_name: this.currentProduct.product_name,
        uom_id: this.currentProduct.uom_id,
        thumbnail: "",
        description: this.currentProduct.description,
        unit_price: this.currentProduct.unit_price,
        stock: this.currentProduct.stock
      })
        .then((response) => {
          console.log("this.currentProduct.uom_id");
          console.log(response.data);
          this.message = "The product was updated successfully!";
        })
        .catch((e) => {
          console.log(e);
        });
    },
    updateUom(uom_id){
      this.currentUomId = uom_id;
    },
    getDisplayUom(uom) {
      return {
        id: uom.id,
        uom_code: uom.uom_code,
        uom_name: uom.uom_name.length > 30 ? uom.uom_name.substr(0, 30) + "..." : uom.uom_name,
      };
    },
    getUomOptions(){
      this.$axios.$get(`uoms`)
        .then((response) => {
          this.uomOptions = response.data.map(this.getDisplayUom);
          console.log("mantap");
          console.log(this.uomOptions);
        })
        .catch((e) => {
          console.log(e);
        });
    },
    getUomById(){
      this.$axios.$get(`uoms/${this.currentProduct.uom_id}/show`)
        .then((response) => {
          this.currentUom = response.data;
          this.currentUomId = this.currentUom.id;
          console.log("mantappppp")
          console.log(this.currentUom);
        })
        .catch((e) => {
          console.log(e);
        });
    },
    getProduct(id) {
      this.$axios.$get(`products/${id}/show`)
        .then((response) => {
          this.currentProduct = response.data;
          this.getUomById();
          console.log(response.data);
        })
        .catch((e) => {
          console.log(e);
        });
    },
  },
  mounted() {
    this.message = "";
    this.getProduct(this.$route.params.id);
    this.getUomOptions();
  },
};
</script>
