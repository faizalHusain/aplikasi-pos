<template>
  <div style="width: 70%; margin: auto; margin-top: 100px">
    <h1 style="font-size: 40px">Sign In</h1>
    <p>Mulai sesi anda</p>
    <v-form>
      <v-text-field
        v-model="userLogin.username"
        label="Email"
        hide-details="auto"
      ></v-text-field>
      <v-text-field
        label="Password"
        type="password"
        v-model="userLogin.password"
      ></v-text-field>

      <v-btn
        v-on:click="logIn"
        color="blue"
        class="rounded-pill py-5"
        style="width: 100%; font-size: 18pt"
        >Sign In</v-btn
      >
    </v-form>
  </div>
</template>
<script>
import axios from "@nuxtjs/axios";
export default {
  layout: "auth",
  data: () => ({
    userLogin: {
      username: "",
      password: "",
    },
  }),
  methods: {
    logIn: async function () {
      try {
        const response = await this.$auth.loginWith("local", {
          data: this.userLogin,
          withCredentials: true,
        });
        console.log(response);
        const user = {
          username: response.data.user.username,
          fullname: response.data.user.fullname,
        };
        this.$auth.$storage.setUniversal("user", user, true);
        this.$router.push("/");
      } catch (error) {
        console.log(error);
      }
    },
  },
};
</script>
