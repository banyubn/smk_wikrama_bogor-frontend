<template>
  <Navbar />
  <v-container max-width="960px">
    <v-btn color="purple" prepend-icon="mdi-arrow-left" class="mb-12" to="/home">Kembali</v-btn>
    <p class="text-h4 font-weight-bold mb-6">Riwayat <span class="text-purple">Pemesananmu</span></p>
    <v-table :loading="loading">
      <thead>
        <tr>
          <th>Order ID</th>
          <th>Total Amount</th>
          <th>Status</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="order in orders">
          <td>{{ order.id }}</td>
          <td>Rp.{{ order.total_amount.toLocaleString("ID-id") }}</td>
          <td>
            <v-chip :color="order.status == 'COMPLETED' ? 'purple' : 'yellow-darken-3'">
              {{ order.status }}
            </v-chip>
          </td>
        </tr>
      </tbody>
    </v-table>
  </v-container>
</template>

<script>
import { API_BASE_URL } from "@/constant";
import axios from "axios";

export default {
  data() {
    return {
      loading: false,
      orders: [],
    };
  },
  methods: {
    // Get orders data
    async getOrders() {
      this.loading = true;

      const token = localStorage.getItem("token");
      try {
        const response = await axios.get(API_BASE_URL + "/orders", {
          headers: {
            Authorization: `Bearer ${token}`,
          },
        });

        this.orders = response.data.data;
      } catch (error) {
      } finally {
        this.loading = false;
      }
    },
  },
  mounted() {
    this.getOrders();
  },
};
</script>
