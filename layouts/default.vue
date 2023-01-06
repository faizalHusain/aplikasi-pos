<template>
  <v-app id="inspire">
    <v-navigation-drawer v-model="drawer" app>
      <v-sheet class="pa-4">
        <v-avatar class="mb-4" color="grey darken-1" size="64"></v-avatar>

        <div style="font-color: white" v-if="this.$auth.user">
          <p>{{ this.$auth.user.username }}</p>
        </div>
      </v-sheet>

      <v-divider></v-divider>

      <v-list>
        <v-list-item v-for="[icon, text] in links" :key="icon" link>
          <v-list-item-icon>
            <v-icon>{{ icon }}</v-icon>
          </v-list-item-icon>

          <v-list-item-content>
            <NuxtLink to="{path : text}"
              ><v-list-item-title>{{ text }}</v-list-item-title></NuxtLink
            >
          </v-list-item-content>
        </v-list-item>
      </v-list>
      <v-list-item>
        <v-list-item-content>
          <v-list-item-title>Master</v-list-item-title>
        </v-list-item-content>
      </v-list-item>
      <v-divider></v-divider>
      <v-list>
        <v-list-item v-for="[icon, text] in links" :key="icon" link>
          <v-list-item-icon>
            <v-icon>{{ icon }}</v-icon>
          </v-list-item-icon>

          <v-list-item-content>
            <v-list-item-title>{{ text }}</v-list-item-title>
          </v-list-item-content>
        </v-list-item>
      </v-list>
      <v-list-item>
        <v-list-item-content>
          <v-list-item-title>Laporan</v-list-item-title>
        </v-list-item-content>
      </v-list-item>
      <v-divider></v-divider>
      <v-list>
        <v-list-item v-for="[icon, text] in links" :key="icon" link>
          <v-list-item-icon>
            <v-icon>{{ icon }}</v-icon>
          </v-list-item-icon>

          <v-list-item-content>
            <v-list-item-title>{{ text }}</v-list-item-title>
          </v-list-item-content>
        </v-list-item>
      </v-list>

      <v-btn style="width: 100%">Logout</v-btn>
    </v-navigation-drawer>
    <v-app-bar app>
      <v-app-bar-nav-icon @click="drawer = !drawer"></v-app-bar-nav-icon>

      <v-toolbar-title>Application</v-toolbar-title>
    </v-app-bar>
    <v-main>
      <v-container class="py-8 px-6" fluid>
        <nuxt />
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
export default {
  data: () => ({
    cards: ["Today", "Yesterday"],
    drawer: null,
    links: [
      ["mdi-inbox-arrow-down", "Dashboard"],
      ["mdi-send", "Sell"],
      ["mdi-delete", "Buy"],
      ["mdi-alert-octagon", "Mutasi"],
    ],
  }),
  methods: {
    // async getUser() {
    //   try {
    //     response = await this.$axios.$get("/me", { withCredentials: true });
    //     console.log(response);
    //     this.user = response.data;
    //   } catch (error) {}
    // },
  },
  mounted() {
    // this.getUser();
  },
};
</script>
