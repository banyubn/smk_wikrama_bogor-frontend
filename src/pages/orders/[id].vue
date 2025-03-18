<template>
  <Navbar />
  <v-container max-width="960px">
    <v-btn color="purple" prepend-icon="mdi-arrow-left" class="mb-12" to="/home">Kembali</v-btn>
    <p class="text-h4 font-weight-bold mb-6">Detail <span class="text-purple">Pemesananmu</span></p>
    <v-card
      v-for="product in orderProducts"
      elevation="0"
      class="d-flex border justify-space-between align-center w-100"
    >
      <div class="d-flex align-center">
        <div class="ml-3 d-flex flex-column">
          <p class="text-h5">{{ product.name }}</p>
          <p class="text-subtitle-1 font-weight-bold">
            Kuantitas : <span class="font-weight-regular">(x{{ product.quantity }})</span>
          </p>
        </div>
      </div>
      <div>
        <p class="mr-3 font-weight-bold">Rp. {{ product.sub_amount.toLocaleString('ID-id') }}</p>
      </div>
    </v-card>
    <v-card class="mt-6 py-6">
        <div class="d-flex justify-space-between text-purple">
            <p class="text-h5 font-weight-bold px-4">Total Harga :</p>
            <p class="text-h5 font-weight-bold px-4">Rp.{{ order.total_amount.toLocaleString('ID-id') }}</p>
        </div>
    </v-card>
  </v-container>
</template>

<script>
import { API_BASE_URL } from "@/constant";
import axios from "axios";

export default {
  data() {
    return {
      loading: false,
      order: [],
      orderProducts: [],
    };
  },
  methods: {
    async getOrders() {
      this.loading = true;
      const token = localStorage.getItem("token");

      try {
        const response = await axios.get(
          API_BASE_URL + "/orders/" + this.getParamsId,
          {
            headers: {
              Authorization: `Bearer ${token}`,
            },
          }
        );

        this.order = response.data.data;
        this.orderProducts = response.data.data.order_products;
      } catch (error) {
      } finally {
        this.loading = false;
      }
    },
  },
  mounted() {
    this.getOrders();
  },
  computed: {
    getParamsId() {
      return this.$route.params.id;
    },
  },
};
</script>
