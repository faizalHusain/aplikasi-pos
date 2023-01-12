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
      <v-btn color="blue" @click="createSupplier()">Add</v-btn>
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
      :items="suppliers_display"
      :search="search"
    >
      <template v-slot:[`item.actions`]="{ item }">
        <v-icon small class="mr-2" @click="editSupplier(item.id)">mdi-pencil</v-icon>
        <v-icon small class="mr-2" @click="deleteSupplier(item.id)">mdi-delete</v-icon>
        <v-icon
          small 
          class="mr-2"
          @click="infoSupplier(item.id)"
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
            {{ supplier_info.email }}
          </v-card-title>
          <v-divider class="my-3"></v-divider>
          <v-card-subtitle class=mt-2>
            Name
          </v-card-subtitle>
          <v-card-text >
            {{ supplier_info.name }}
          </v-card-text>
          <v-card-subtitle class=mt-2>
            Phone Number
          </v-card-subtitle>
          <v-card-text >
            {{ supplier_info.phone_number }}
          </v-card-text>
          <v-card-subtitle class=mt-2>
            Address
          </v-card-subtitle>
          <v-card-text >
            {{ supplier_info.address }}
          </v-card-text>
          <v-card-subtitle class=mt-2>
            Joined At
          </v-card-subtitle>
          <v-card-text >
            {{ supplier_info.created_at }}
          </v-card-text>

          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn
              color="warning"
              text
              @click="editSupplier(supplier_info.id)"
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
      suppliers_display: [],
      suppliers: [],
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
      supplier_info : {}
    };
  },
  methods: {
    infoSupplier(id){
      var supplier = this.suppliers.filter(obj => {
        return obj.id === id
      })[0]
      this.supplier_info = {
        id: supplier.id,
        name: supplier.name,
        email: supplier.email,
        phone_number: supplier.phone_number,
        address: supplier.address.length > 30 ? supplier.address.substr(0, 30) + "..." : supplier.address,
        created_at: supplier.created_at.split(" ")[0],
      } 
      console.log(this.supplier_info)
      this.dialog=true;
    },
    deleteSupplier(id) {
      this.$axios.$delete(`suppliers/delete`, {data: {
        "id": id,
      }})
        .then(() => {
          this.message = "Supplier deleted succesfully";
          this.refreshList();
        })
        .catch((e) => {
          console.log(e);
        });
    },
    editSupplier(id) {
      this.$router.push(`supplier/edit/${id}`);
    },
    createSupplier() {
      this.$router.push(`supplier/create`);
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
    retrieveSuppliers() {
      this.$axios.$get("suppliers")
        .then((response) => {
          this.suppliers_display = response.data.map(this.getDisplaySupplier);
          this.suppliers = response.data
          console.log(response.data);
        })
        .catch((e) => {
          console.log(e);
        });
    },
    refreshList(){
      this.retrieveSuppliers();
    }
    // getSupplier: async function () {
    //   const response = await this.$axios.$get("suppliers");
    //   console.log(response);
    // },
  },
  mounted() {
    this.retrieveSuppliers();
    this.message="";
  },
};
</script>
