<template>
  <VueLoading v-model:active="isLoading"/>
  <div>這裡是前台^^</div>
  <nav
    class="my-5 d-flex justify-content-center align-items-center py-3 text-white"
  >
    <router-link
      class="link-warning mx-2 fs-4 fw-bold router-link-active"
      to="/home"
      >首頁</router-link
    >|
    <router-link
      class="link-warning mx-2 fs-4 fw-bold router-link-active"
      to="/product"
      >前台產品</router-link
    >|
    <router-link
      class="link-warning mx-2 fs-4 fw-bold router-link-active"
      to="/cart"
      >購物車</router-link
    >|
    <router-link
      class="link-warning mx-2 fs-4 fw-bold router-link-active"
      to="/admin/products"
      >後台產品</router-link
    >
  </nav>
  <RouterView />
</template>

<script>
import { RouterView, RouterLink } from "vue-router";

export default {
  data() {
    return {
      isLoading: false,
    };
  },
  components: {
    RouterView,
    RouterLink,
  },
  mounted() {
    this.isLoading = true;
    setTimeout(() => {
      this.isLoading = false;
    }, 2000);
  },
};
</script>
