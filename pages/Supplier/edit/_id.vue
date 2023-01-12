<template>
  <div v-if="currentSupplier" class="edit-form py-3">
    <p class="headline">Edit Supplier</p>

    <v-form ref="form" lazy-validation>
        <v-text-field
          v-model="currentSupplier.name"
          :rules="[(v) => !!v || 'Name is Required']"
          label="Name"
          required
        ></v-text-field>

        <v-text-field
          v-model="currentSupplier.phone_number"
          :rules="[(v) => !!v || 'Phone Number is Required']"
          label="Phone Number"
          required
        ></v-text-field>

        <v-text-field
          v-model="currentSupplier.address"
          :rules="[(v) => !!v || 'Address is Required']"
          label="Address"
          required
        ></v-text-field>

      <v-divider class="my-5"></v-divider>

      <v-btn color="warning" small class="mr-2" :to="{name: 'Supplier'}">
        Back to Suppliers
      </v-btn>

      <v-btn color="error" small class="mr-2" @click="deleteSupplier">
        Delete
      </v-btn>

      <v-btn color="success" small @click="updateSupplier">
        Update
      </v-btn>
      

    </v-form>

    <p class="mt-3">{{ message }}</p>
  </div>

  <div v-else>
    <p>Supplier not found</p>
  </div>
</template>

<script>
export default {
  layout: "default",
  data() {
    return {
      currentSupplier: null,
      message: "",
    };
  },
  methods: {
    deleteSupplier() {
      this.$axios.$delete(`suppliers/delete`, {data: {
        "id": this.currentSupplier.id,
      }})
        .then((response) => {
          console.log(response.data);
          this.$router.push("/supplier");
        })
        .catch((e) => {
          console.log(e);
        });
    },
    updateSupplier() {
      this.currentSupplier.supplier_id = this.currentSupplierId;
      this.$axios.$put(`suppliers/update`, this.currentSupplier)
        .then((response) => {
          console.log(response.data);
          this.message = "The supplier was updated successfully!";
        })
        .catch((e) => {
          console.log(e);
        });
    },
    getDisplaySupplier(supplier) {
      return {
        id: supplier.id,
        name: supplier.name,
        email: supplier.email,
        phone_number: supplier.phone_number,
        address: supplier.address.length > 30 ? supplier.address.substr(0, 30) + "..." : supplier.address,
        created_at: supplier.created_at.split(" ")[0],
      };
    },
    getSupplier(id) {
      this.$axios.$get(`suppliers/${id}/show`)
        .then((response) => {
          this.currentSupplier = response.data;
          this.getSupplierById();
          console.log(response.data);
        })
        .catch((e) => {
          console.log(e);
        });
    },
  },
  mounted() {
    this.message = "";
    this.getSupplier(this.$route.params.id);
  },
};
</script>
