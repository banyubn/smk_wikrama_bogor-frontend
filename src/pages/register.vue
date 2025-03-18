<template>
  <!-- <v-text-field>
    </v-text-field> -->
  <v-row class="h-100">
    <v-col md="3" class="bg-purple"></v-col>
    <v-col md="9" class="bg-white px-16 login-form">
      <p class="text-h5 mb-12">
        Makan <br />
        <span class="font-weight-bold"> Yuk </span>
      </p>
      <p class="text-h4 font-weight-bold mb-6">Daftar dulu yuk!</p>
      <v-form @submit.prevent="submit">
        <v-text-field
          :loading="loading"
          variant="underlined"
          label="Nama"
          v-model="name"
          class="mb-2"
        >
        </v-text-field>
        <v-text-field
          :loading="loading"
          variant="underlined"
          label="Email"
          v-model="email"
          class="mb-2"
        >
        </v-text-field>
        <v-select
          v-model="role"
          label="Pilih role kamu"
          :items="['PENJUAL', 'GUEST']"
          variant="underlined"
          class="mb-2"
        ></v-select>
        <v-text-field
          :type="showPassword ? 'text' : 'password'"
          :loading="loading"
          :append-inner-icon="showPassword ? 'mdi-eye' : 'mdi-eye'"
          variant="underlined"
          label="Password"
          v-model="password"
          class="mb-2"
          v-on:click:append-inner="showPassword = !showPassword"
        >
        </v-text-field>
        <div class="d-flex justify-space-between align-center">
          <v-btn :loading="loading" type="submit" color="purple">Daftar</v-btn>
          <v-btn
            :loading="loading"
            variant="plain"
            to="/"
            class="text-subtitle-1 pa-0 ma-0 mt-2"
            >Sudah punya akun? Langsung masuk</v-btn
          >
        </div>
      </v-form>
    </v-col>
  </v-row>
</template>

<script>
import { API_BASE_URL } from "@/constant";
import axios from "axios";

export default {
  data() {
    return {
      showPassword: false,
      loading: false,
      name: "",
      email: "",
      role: "",
      password: "",
    };
  },
  methods: {
    async submit() {
      this.loading = true;
      try {
        const payload = {
          name: this.name,
          email: this.email,
          password: this.password,
          role: this.role,
        };

        const response = await axios.post(
          API_BASE_URL + "/users/register",
          payload
        );

        localStorage.setItem("token", response.data.token);

        if (response.data.data.role == "GUEST") {
          this.$router.push({ path: "/home" });
        } else {
          this.$router.push({ path: "/manage-products" });
        }
      } catch (error) {
      } finally {
        this.loading = false;
      }
    },
  },
};
</script>

<style scoped>
.login-form {
  margin-top: 5%;
}
</style>
