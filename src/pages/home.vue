<template>
  <Navbar />
  <v-snackbar v-model="snackbar" color="purple" timeout="3000">
    {{ snackbarText }}

    <template v-slot:actions>
      <v-btn color="white" variant="text" @click="snackbar = false">
        Close
      </v-btn>
    </template>
  </v-snackbar>
  <v-container max-width="960px" style="margin-bottom: 70px;">
    <div class="w-100 pa-12 background-banner rounded-xl mb-12">
      <v-row>
        <v-col sm="12" md="6">
          <p class="text-h4 text-white font-weight-bold">
            Ayo, jajan makanan khas Bogor
          </p>
          <p class="text-body-2 my-2 text-white">#makanankhasbogor</p>
          <v-text-field
            prepend-inner-icon="mdi-magnify"
            class="mt-6"
            rounded="pill"
            label="Cari produk ..."
            variant="solo"
            density="compact"
          >
          </v-text-field>
        </v-col>
      </v-row>
    </div>
    <p class="text-h4 mb-8 font-weight-bold">
      Makanan Lezat Khas <span class="text-purple">Bogor</span>
    </p>
    <v-row>
      <v-col sm="12" md="6" v-for="product in products">
        <v-card elevation="0">
          <v-img
            class="rounded-lg"
            height="250px"
            cover
            :src="'http://127.0.0.1:8000/' + product.image"
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
      snackbar: false,
      snackbarText: "",
      loading: false,
      cart: [],
      products: [],
    };
  },
  methods: {
    // Checkout products
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

        const id = response.data.data.id;
        this.$router.push({ path: "/orders/" + id });
      } catch (error) {
        this.snackbarText = "Terjadi kesaslahan!";

        setTimeout(function () {
          this.snackbarText = "";
        }, 5000);

        this.snackbar = true;
      } finally {
        this.loading = false;
      }
    },
    // Get products from API
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

        console.log(this.products);
      } catch (error) {
        this.snackbarText = "Terjadi kesaslahan!";

        setTimeout(function () {
          this.snackbarText = "";
        }, 5000);

        this.snackbar = true;
      } finally {
        this.loading = false;
      }
    },
    // Count how many product has been clicked
    productAmount(productId) {
      const productExists = this.cart.find(
        (item) => item.product_id === productId
      );

      return productExists.quantity;
    },
    // Check if product already exists
    checkProduct(productId) {
      const productExists = this.cart.find(
        (item) => item.product_id === productId
      );

      return productExists?.quantity;
    },
    // Decrease products from cart
    minProduct(productId) {
      const productExists = this.cart.find(
        (item) => item.product_id === productId
      );

      if (productExists) {
        productExists.quantity -= 1;
      }
    },
    // Increase products from cart
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

.background-banner {
  background: url("/src/assets/doodle_background.png");
}
</style>
