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
                <p>Nama</p>
                <p class="text-subtitle-2">emailemail@gmail.com</p>
              </v-list-item-title>
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
export default {
  methods: {
    logout() {
      localStorage.removeItem("token");

      this.$router.push({ path: "/" });
    },
  },
};
</script>
