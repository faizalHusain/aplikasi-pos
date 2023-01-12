<template>
  <div class="submit-form mt-3 mx-auto">
    <p class="headline">Add Customer</p>

    <div v-if="!submitted">
      <v-form ref="form" lazy-validation>
        <v-text-field
          v-model="customer.email"
          :rules="[(v) => !!v || 'Email is Required']"
          label="Email"
          required
        ></v-text-field>
        
        <v-text-field
          v-model="customer.name"
          :rules="[(v) => !!v || 'Name is Required']"
          label="Name"
          required
        ></v-text-field>

        <v-text-field
          v-model="customer.phone_number"
          :rules="[(v) => !!v || 'Phone Number is Required']"
          label="Phone Number"
          required
        ></v-text-field>

        <v-text-field
          v-model="customer.address"
          :rules="[(v) => !!v || 'Address is Required']"
          label="Address"
          required
        ></v-text-field>
        
      </v-form>
      <v-btn color="warning" class="mt-3" :to="{name: 'Customer'}">
        Back to Customers
      </v-btn>
      <v-btn color="primary" class="mt-3" @click="saveCustomer">Submit</v-btn>
    </div>

    <div v-else>
      <v-card class="mx-auto">
        <v-card-title>
          Submitted successfully!
        </v-card-title>

        <v-card-subtitle>
          Click the button to add new Customer or get back to customers.
        </v-card-subtitle>

        <v-card-actions>
          <v-btn color="success" @click="newCustomer">Add</v-btn>
          <v-btn color="warning" :to="{name: 'Customer'}">Back to Customers</v-btn>
        </v-card-actions>
      </v-card>
    </div>
  </div>
</template>

<script>

export default {
  name: "add-customer",
  data() {
    return {
      customer:{
        email: "",
        name: "",
        phone_number: "",
        address: "",
      },
      submitted: false,
    };
  },
  methods: {
    saveCustomer() {
      var data = {
        email: this.customer.email,
        name: this.customer.name,
        phone_number: this.customer.phone_number,
        address: this.customer.address,
      };

      this.$axios.$post(`customers/create`, data)
        .then((response) => {
          this.customer.id = response.data.id;
          this.submitted = true;
        })
        .catch((e) => {
          console.log(e);
        });
    },
    newCustomer() {
      this.submitted = false;
      this.customer = {};
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
