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
      <v-btn color="blue" @click="createCustomer()">Add</v-btn>
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
      :items="customers_display"
      :search="search"
    >
      <template v-slot:[`item.actions`]="{ item }">
        <v-icon small class="mr-2" @click="editCustomer(item.id)">mdi-pencil</v-icon>
        <v-icon small class="mr-2" @click="deleteCustomer(item.id)">mdi-delete</v-icon>
        <v-icon
          small 
          class="mr-2"
          @click="infoCustomer(item.id)"
        >
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
            {{ customer_info.email }}
          </v-card-title>
          <v-divider class="my-3"></v-divider>
          <v-card-subtitle class=mt-2>
            Name
          </v-card-subtitle>
          <v-card-text >
            {{ customer_info.name }}
          </v-card-text>
          <v-card-subtitle class=mt-2>
            Phone Number
          </v-card-subtitle>
          <v-card-text >
            {{ customer_info.phone_number }}
          </v-card-text>
          <v-card-subtitle class=mt-2>
            Address
          </v-card-subtitle>
          <v-card-text >
            {{ customer_info.address }}
          </v-card-text>
          <v-card-subtitle class=mt-2>
            Joined At
          </v-card-subtitle>
          <v-card-text >
            {{ customer_info.created_at }}
          </v-card-text>

          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn
              color="warning"
              text
              @click="editCustomer(customer_info.id)"
            >
              Edit
            </v-btn>
            <v-btn
              color="primary"
              text
              @click="dialog = false"
            >
              OK
            </v-btn>
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
      customers_display: [],
      customers: [],
      message: "",
      title: "",
      search: "",
      dialog: false,
      headers: [
        { text: "Name", align: "start", sortable: true, value: "name", },
        { text: "Email", value: "email", sortable: true, },
        { text: "Phone Number", value: "phone_number", sortable: false, },
        { text: "Address", value: "address", sortable: true, },
        { text: "Joined At", value: "created_at", sortable: true, },
        { text: "Actions", value: "actions", sortable: false },
      ],
      customer_info : {}
    };
  },
  methods: {
    infoCustomer(id){
      var customer = this.customers.filter(obj => {
        return obj.id === id
      })[0]
      this.customer_info = {
        id: customer.id,
        name: customer.name,
        email: customer.email,
        phone_number: customer.phone_number,
        address: customer.address.length > 30 ? customer.address.substr(0, 30) + "..." : customer.address,
        created_at: customer.created_at.split(" ")[0],
      } 
      console.log(this.customer_info)
      this.dialog=true;
    },
    deleteCustomer(id) {
      this.$axios.$delete(`customers/delete`, {data: {
        "id": id,
      }})
        .then(() => {
          this.message = "Customer deleted succesfully";
          this.refreshList();
        })
        .catch((e) => {
          console.log(e);
        });
    },
    editCustomer(id) {
      this.$router.push(`customer/edit/${id}`);
    },
    createCustomer() {
      this.$router.push(`customer/create`);
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
    retrieveCustomers() {
      this.$axios.$get("customers")
        .then((response) => {
          this.customers_display = response.data.map(this.getDisplayCustomer);
          this.customers = response.data
          console.log(response.data);
        })
        .catch((e) => {
          console.log(e);
        });
    },
    refreshList(){
      this.retrieveCustomers();
    }
    // getCustomer: async function () {
    //   const response = await this.$axios.$get("customers");
    //   console.log(response);
    // },
  },
  mounted() {
    this.retrieveCustomers();
    this.message="";
  },
};
</script>
