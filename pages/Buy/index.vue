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
      <v-btn color="blue" @click="createBuy()">Add</v-btn>
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
    <v-data-table :headers="headers" :items="buys_display" :search="search">
      <template v-slot:[`item.actions`]="{ item }">
        <!-- <v-icon small class="mr-2" @click="editSell(item.id)">mdi-pencil</v-icon>
        <v-icon small class="mr-2" @click="deleteSell(item.id)"
          >mdi-delete</v-icon
        > -->
        <v-icon small class="mr-2" @click="infoSell(item.id)">
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
          <v-card-title> Details </v-card-title>
          <v-card-text>
            <v-simple-table class="mt-0">
              <template v-slot:default>
                <thead>
                  <tr>
                    <th class="text-left">Produk</th>
                    <th class="text-left">Quantity</th>
                    <th class="text-left">Unit Price</th>
                    <th class="text-left">Total</th>
                  </tr>
                </thead>
                <tbody>
                  <tr v-for="item in sells_item" :key="item.id">
                    <td>{{ item.product_name }}</td>
                    <td>{{ item.quantity }}</td>
                    <td>{{ item.unit_price }}</td>
                    <td>{{ item.total_cost }}</td>
                  </tr>
                </tbody>
              </template>
            </v-simple-table>
          </v-card-text>
          <!-- <v-data-table :headers="headers" :items="sells_item" :search="search">
            <template v-slot:[`item.actions`]="{ item }"> </template>
          </v-data-table> -->
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
      buys_display: [],
      buys: [],
      sells_item: [],
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
        { text: "Actions", value: "actions", sortable: false },
      ],
      uom_info: {
        uom_code: "",
        uom_name: "",
      },
    };
  },
  methods: {
    async infoSell(id) {
      const response = await this.$axios.get(`/buys/list?order_id=${id}`);
      this.sells_item = response.data.data;
      console.log(response.data.data);
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
    createBuy() {
      this.$router.push(`buy/create`);
    },
    getDisplayBuy(buys) {
      return {
        id: buys.id,
        name:
          buys.name.length > 30 ? buys.name.substr(0, 30) + "..." : buys.name,
        total_cost: buys.total_cost,
        created_at: buys.created_at.split(" ")[0],
      };
    },
    retrieveBuy() {
      this.$axios
        .$get("buy_orders/list")
        .then((response) => {
          console.log(response.data);
          this.buys_display = response.data.map(this.getDisplayBuy);
          this.buys = response.data;
          console.log(response.data);
        })
        .catch((e) => {
          console.log(e);
        });
    },
    refreshList() {
      this.retrieveBuy();
    },
    // getUom: async function () {
    //   const response = await this.$axios.$get("uoms");
    //   console.log(response);
    // },
  },
  mounted() {
    this.retrieveBuy();
    this.message = "";
  },
};
</script>
