<template>
  <div v-if="currentCustomer" class="edit-form py-3">
    <p class="headline">Edit Customer</p>

    <v-form ref="form" lazy-validation>
        <v-text-field
          v-model="currentCustomer.name"
          :rules="[(v) => !!v || 'Name is Required']"
          label="Name"
          required
        ></v-text-field>

        <v-text-field
          v-model="currentCustomer.phone_number"
          :rules="[(v) => !!v || 'Phone Number is Required']"
          label="Phone Number"
          required
        ></v-text-field>

        <v-text-field
          v-model="currentCustomer.address"
          :rules="[(v) => !!v || 'Address is Required']"
          label="Address"
          required
        ></v-text-field>

      <v-divider class="my-5"></v-divider>

      <v-btn color="warning" small class="mr-2" :to="{name: 'Customer'}">
        Back to Customers
      </v-btn>

      <v-btn color="error" small class="mr-2" @click="deleteCustomer">
        Delete
      </v-btn>

      <v-btn color="success" small @click="updateCustomer">
        Update
      </v-btn>
      

    </v-form>

    <p class="mt-3">{{ message }}</p>
  </div>

  <div v-else>
    <p>Customer not found</p>
  </div>
</template>

<script>
export default {
  layout: "default",
  data() {
    return {
      currentCustomer: null,
      message: "",
    };
  },
  methods: {
    deleteCustomer() {
      this.$axios.$delete(`customers/delete`, {data: {
        "id": this.currentCustomer.id,
      }})
        .then((response) => {
          console.log(response.data);
          this.$router.push("/customer");
        })
        .catch((e) => {
          console.log(e);
        });
    },
    updateCustomer() {
      this.currentCustomer.customer_id = this.currentCustomerId;
      this.$axios.$put(`customers/update`, this.currentCustomer)
        .then((response) => {
          console.log(response.data);
          this.message = "The customer was updated successfully!";
        })
        .catch((e) => {
          console.log(e);
        });
    },
    getDisplayCustomer(customer) {
      return {
        id: customer.id,
        name: customer.name,
        email: customer.email,
        phone_number: customer.phone_number,
        address: customer.address.length > 30 ? customer.address.substr(0, 30) + "..." : customer.address,
        created_at: customer.created_at.split(" ")[0],
      };
    },
    getCustomer(id) {
      this.$axios.$get(`customers/${id}/show`)
        .then((response) => {
          this.currentCustomer = response.data;
          this.getCustomerById();
          console.log(response.data);
        })
        .catch((e) => {
          console.log(e);
        });
    },
  },
  mounted() {
    this.message = "";
    this.getCustomer(this.$route.params.id);
  },
};
</script>
