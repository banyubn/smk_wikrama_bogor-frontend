<template>
  <Navbar />
  <v-container max-width="960px">
    <v-row>
      <v-col sm="12" md="6" v-for="product in products">
        <v-card elevation="0">
          <v-img
            class="rounded-lg"
            height="250px"
            cover
            src="/src/assets/lapis_talas.jpg"
          >
          </v-img>
          <v-card-title class="my-0">{{ product.name }}</v-card-title>
          <div class="">
            <p class="px-4 text-subtitle-2 font-weight-regular">
              {{ product.description }}
            </p>
          </div>
          <div class="d-flex">
            <p class="px-4 mt-6 font-weight-bold text-h6">
              {{ product.price.toLocaleString("ID-id") }}
            </p>
          </div>
          <v-divider class="my-2"> </v-divider>
          <v-btn
            v-if="!checkProduct(product.id)"
            @click="addProduct(product.id)"
            rounded="pill"
            color="purple"
            block
            variant="outlined"
            >+ Beli</v-btn
          >
          <div v-else class="d-flex justify-space-between">
            <v-btn
              variant="outlined"
              rounded="xl"
              color="yellow"
              class="text-h5"
              @click="minProduct(product.id)"
              >-</v-btn
            >
            <p class="text-h5">{{ productAmount(product.id) }}</p>
            <v-btn
              variant="outlined"
              rounded="xl"
              color="purple"
              class="text-h5"
              @click="addProduct(product.id)"
              >+</v-btn
            >
          </div>
        </v-card>
      </v-col>
    </v-row>

    <v-slide-y-reverse-transition>
      <v-card
        color="purple-lighten-2 pa-4"
        v-if="cart.length > 0"
        class="popup-card"
      >
        <div class="d-flex justify-space-between align-center">
          <v-icon>mdi-cart</v-icon>
          <v-btn append-icon="mdi-send" @click="checkout">Beli</v-btn>
        </div>
      </v-card>
    </v-slide-y-reverse-transition>
  </v-container>
</template>

<script>
import { API_BASE_URL } from "@/constant";
import axios from "axios";

export default {
  data() {
    return {
      loading: false,
      cart: [],
      products: [],
    };
  },
  methods: {
    async checkout() {
      this.loading = true;

      const token = localStorage.getItem("token");
      try {
        const payload = this.cart;
        const response = await axios.post(
          API_BASE_URL + "/orders",
          {
            products: payload,
          },
          {
            headers: {
              Authorization: `Bearer ${token}`,
            },
          }
        );

        console.log(response.data);
      } catch {
      } finally {
        this.loading = false;
      }
    },
    async getProducts() {
      this.loading = true;
      const token = localStorage.getItem("token");
      try {
        const response = await axios.get(API_BASE_URL + "/products", {
          headers: {
            Authorization: `Bearer ${token}`,
          },
        });

        this.products = response.data.data;
      } catch (error) {
      } finally {
        this.loading = false;
      }
    },
    productAmount(productId) {
      const productExists = this.cart.find(
        (item) => item.product_id === productId
      );

      return productExists.quantity;
    },
    checkProduct(productId) {
      const productExists = this.cart.find(
        (item) => item.product_id === productId
      );

      return productExists?.quantity;
    },
    minProduct(productId) {
      const productExists = this.cart.find(
        (item) => item.product_id === productId
      );

      if (productExists) {
        productExists.quantity -= 1;
      }
    },
    addProduct(productId) {
      const productExists = this.cart.find(
        (item) => item.product_id === productId
      );

      if (productExists) {
        productExists.quantity += 1;
      } else {
        const data = {
          product_id: productId,
          quantity: 1,
        };

        this.cart.push(data);
      }
    },
  },
  mounted() {
    this.getProducts();
  },
};
</script>

<style scoped>
.popup-card {
  position: fixed;
  width: 100%;
  bottom: 0;
  left: 0;
}
</style>
