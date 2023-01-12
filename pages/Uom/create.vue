<template>
  <div class="submit-form mt-3 mx-auto">
    <p class="headline">Add Uom</p>

    <div v-if="!submitted">
      <v-form ref="form" lazy-validation>
        <v-text-field
          v-model="uom.uom_code"
          :rules="[(v) => !!v || 'Uom Code is Required']"
          label="Uom Code"
          required
        ></v-text-field>

        <v-text-field
          v-model="uom.uom_name"
          :rules="[(v) => !!v || 'Uom Name is Required']"
          label="Uom Name"
          required
        ></v-text-field>
      </v-form>
      <v-btn color="warning" class="mt-3" :to="{name: 'Uom'}">
        Back to Uoms
      </v-btn>
      <v-btn color="primary" class="mt-3" @click="saveUom">Submit</v-btn>
    </div>

    <div v-else>
      <v-card class="mx-auto">
        <v-card-title>
          Submitted successfully!
        </v-card-title>

        <v-card-subtitle>
          Click the button to add new Uom or get back to uoms.
        </v-card-subtitle>

        <v-card-actions>
          <v-btn color="success" @click="newUom">Add</v-btn>
          <v-btn color="warning" :to="{name: 'Uom'}">Back to Uoms</v-btn>
        </v-card-actions>
      </v-card>
    </div>
  </div>
</template>

<script>

export default {
  name: "add-uom",
  data() {
    return {
      uom:{
        uom_code: "",
        uom_name: "",
      },
      submitted: false,
    };
  },
  methods: {
    saveUom() {
      var data = {
        uom_code: this.uom.uom_code,
        uom_name: this.uom.uom_name,
      };

      this.$axios.$post(`uoms/create`, data)
        .then((response) => {
          this.uom.id = response.data.id;
          this.submitted = true;
        })
        .catch((e) => {
          console.log(e);
        });
    },
    newUom() {
      this.submitted = false;
      this.uom = {};
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
