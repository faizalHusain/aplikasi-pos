<template>
  <div v-if="currentUom" class="edit-form py-3">
    <p class="headline">Edit Uom</p>

    <v-form ref="form" lazy-validation>
      <v-text-field
        v-model="currentUom.uom_code"
        :rules="[(v) => !!v || 'Uom Code is Required']"
        label="Uom Code"
        required
      ></v-text-field>

      <v-text-field
        v-model="currentUom.uom_name"
        :rules="[(v) => !!v || 'Uom Name is Required']"
        label="Uom Name"
        required
      ></v-text-field>

      <v-divider class="my-5"></v-divider>

      <v-btn color="warning" small class="mr-2" :to="{name: 'Uom'}">
        Back to Uoms
      </v-btn>

      <v-btn color="error" small class="mr-2" @click="deleteUom">
        Delete
      </v-btn>

      <v-btn color="success" small @click="updateUom">
        Update
      </v-btn>
      

    </v-form>

    <p class="mt-3">{{ message }}</p>
  </div>

  <div v-else>
    <p>Uom not found</p>
  </div>
</template>

<script>
export default {
  layout: "default",
  data() {
    return {
      currentUom: null,
      message: "",
    };
  },
  methods: {
    deleteUom() {
      this.$axios.$delete(`uoms/delete`, {data: {
        "id": this.currentUom.id,
      }})
        .then((response) => {
          console.log(response.data);
          this.$router.push("/uom");
        })
        .catch((e) => {
          console.log(e);
        });
    },
    updateUom() {
      this.currentUom.uom_id = this.currentUomId;
      this.$axios.$put(`uoms/update`, {
        id: this.currentUom.id,
        uom_code: this.currentUom.uom_code,
        uom_name: this.currentUom.uom_name,
      })
        .then((response) => {
          console.log(response.data);
          this.message = "The uom was updated successfully!";
        })
        .catch((e) => {
          console.log(e);
        });
    },
    getDisplayUom(uom) {
      return {
        id: uom.id,
        uom_code: uom.uom_code,
        uom_name: uom.uom_name.length > 30 ? uom.uom_name.substr(0, 30) + "..." : uom.uom_name,
      };
    },
    getUom(id) {
      this.$axios.$get(`uoms/${id}/show`)
        .then((response) => {
          this.currentUom = response.data;
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
    this.getUom(this.$route.params.id);
  },
};
</script>
