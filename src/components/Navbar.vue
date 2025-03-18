<template>
  <v-toolbar color="white">
    <v-toolbar-title>
      <p>Makan<span class="font-weight-bold">Yuk</span></p>
    </v-toolbar-title>
    <v-spacer></v-spacer>
    <v-btn variant="plain">
      <v-avatar color="purple">
        <v-img src="/src/assets/user_profile.png"> </v-img>

        <v-menu activator="parent">
          <v-list>
            <v-list-item>
              <v-list-item-title>
                <p>{{ user.name }}</p>
                <p class="text-subtitle-2">{{ user.email }}</p>
              </v-list-item-title>
            </v-list-item>
            <v-divider></v-divider>
            <v-list-item>
              <v-btn variant="plain" class="ma-0 pa-0 text-body-2" to="/history"
                >Riwayat Pemesanan</v-btn
              >
            </v-list-item>
            <v-list-item>
              <v-btn color="red" prepend-icon="mdi-logout" @click="logout"
                >Logout</v-btn
              >
            </v-list-item>
          </v-list>
        </v-menu>
      </v-avatar>
    </v-btn>
  </v-toolbar>
</template>

<script>
import { API_BASE_URL } from "@/constant";
import axios from "axios";

export default {
  data() {
    return {
      user: [],
    };
  },
  methods: {
    // Remove token from logged in user
    logout() {
      localStorage.removeItem("token");

      this.$router.push({ path: "/" });
    },
    // Takes logged in user data
    async getMe() {
      const token = localStorage.getItem("token");
      try {
        const response = await axios.get(API_BASE_URL + "/users/profile", {
          headers: {
            Authorization: `Bearer ${token}`,
          },
        });
        this.user = response.data.data;
      } catch (error) {}
    },
  },
  mounted() {
    this.getMe();
  },
};
</script>
