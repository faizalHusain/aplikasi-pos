<template>
  <div class="submit-form mt-3 mx-auto">
    <p class="headline">Add Supplier</p>

    <div v-if="!submitted">
      <v-form ref="form" lazy-validation>
        <v-text-field
          v-model="supplier.email"
          :rules="[(v) => !!v || 'Email is Required']"
          label="Email"
          required
        ></v-text-field>
        
        <v-text-field
          v-model="supplier.name"
          :rules="[(v) => !!v || 'Name is Required']"
          label="Name"
          required
        ></v-text-field>

        <v-text-field
          v-model="supplier.phone_number"
          :rules="[(v) => !!v || 'Phone Number is Required']"
          label="Phone Number"
          required
        ></v-text-field>

        <v-text-field
          v-model="supplier.address"
          :rules="[(v) => !!v || 'Address is Required']"
          label="Address"
          required
        ></v-text-field>
        
      </v-form>
      <v-btn color="warning" class="mt-3" :to="{name: 'Supplier'}">
        Back to Suppliers
      </v-btn>
      <v-btn color="primary" class="mt-3" @click="saveSupplier">Submit</v-btn>
    </div>

    <div v-else>
      <v-card class="mx-auto">
        <v-card-title>
          Submitted successfully!
        </v-card-title>

        <v-card-subtitle>
          Click the button to add new Supplier or get back to suppliers.
        </v-card-subtitle>

        <v-card-actions>
          <v-btn color="success" @click="newSupplier">Add</v-btn>
          <v-btn color="warning" :to="{name: 'Supplier'}">Back to Suppliers</v-btn>
        </v-card-actions>
      </v-card>
    </div>
  </div>
</template>

<script>

export default {
  name: "add-supplier",
  data() {
    return {
      supplier:{
        email: "",
        name: "",
        phone_number: "",
        address: "",
      },
      submitted: false,
    };
  },
  methods: {
    saveSupplier() {
      var data = {
        email: this.supplier.email,
        name: this.supplier.name,
        phone_number: this.supplier.phone_number,
        address: this.supplier.address,
      };

      this.$axios.$post(`suppliers/create`, data)
        .then((response) => {
          this.supplier.id = response.data.id;
          this.submitted = true;
        })
        .catch((e) => {
          console.log(e);
        });
    },
    newSupplier() {
      this.submitted = false;
      this.supplier = {};
    },
  },
  mounted() {
    this.message = "";
  },
};
</script>

<style>
.submit-form {
  max-width: 300px;
}
</style>
