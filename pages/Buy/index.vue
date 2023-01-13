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
      <v-btn color="blue" @click="createSell()">Add</v-btn>
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
    <v-data-table :headers="headers" :items="sells_display" :search="search">
      <template v-slot:[`item.actions`]="{ item }">
        <!-- <v-icon small class="mr-2" @click="editSell(item.id)">mdi-pencil</v-icon>
        <v-icon small class="mr-2" @click="deleteSell(item.id)"
          >mdi-delete</v-icon
        > -->
        <!-- <v-icon small class="mr-2" @click="infoSell(item.id)">
          mdi-information-outline
        </v-icon> -->
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
            {{ uom_info.uom_name }}
          </v-card-title>
          <v-divider class="my-3"></v-divider>
          <v-card-subtitle class="mt-2"> Code </v-card-subtitle>
          <v-card-text>
            {{ uom_info.uom_code }}
          </v-card-text>

          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="warning" text @click="editUom(uom_info.id)">
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
      sells_display: [],
      sells: [],
      message: "",
      title: "",
      search: "",
      dialog: false,
      headers: [
        {
          text: "Name",
          align: "start",
          sortable: false,
          value: "name",
        },
        { text: "Total Cost", value: "total_cost", sortable: true },
        { text: "Transaction Date", value: "created_at", sortable: false },
      ],
      uom_info: {
        uom_code: "",
        uom_name: "",
      },
    };
  },
  methods: {
    infoUom(id) {
      var uom = this.uoms.filter((obj) => {
        return obj.id === id;
      })[0];
      this.uom_info = {
        id: uom.id,
        uom_code: uom.uom_code,
        uom_name: uom.uom_name,
      };
      console.log(this.uom_info);
      this.dialog = true;
    },
    deleteUom(id) {
      this.$axios
        .$delete(`uoms/delete`, {
          data: {
            id: id,
          },
        })
        .then(() => {
          this.message = "Uom deleted succesfully";
          this.refreshList();
        })
        .catch((e) => {
          console.log(e);
        });
    },
    editUom(id) {
      this.$router.push(`uom/edit/${id}`);
    },
    createSell() {
      this.$router.push(`buy/create`);
    },
    getDisplayUom(sells) {
      return {
        id: sells.id,
        name:
          sells.name.length > 30
            ? sells.name.substr(0, 30) + "..."
            : sells.name,
        total_cost: sells.total_cost,
        created_at: sells.created_at,
      };
    },
    retrieveUoms() {
      this.$axios
        .$get("buy_orders/list")
        .then((response) => {
          this.sells_display = response.data.map(this.getDisplayUom);
          this.sells = response.data;
          console.log(response.data);
        })
        .catch((e) => {
          console.log(e);
        });
    },
    refreshList() {
      this.retrieveUoms();
    },
    // getUom: async function () {
    //   const response = await this.$axios.$get("uoms");
    //   console.log(response);
    // },
  },
  mounted() {
    this.retrieveUoms();
    this.message = "";
  },
};
</script>
