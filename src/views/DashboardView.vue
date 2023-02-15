<template>
  <div>這是後台頁面</div>
  <nav
    class="my-5 d-flex justify-content-center align-items-center py-3 text-white"
  >
    <router-link
      class="link-secondary mx-2 fs-4 fw-bold router-link-active"
      to="/home"
      >回到前台</router-link
    >|
    <router-link
      class="link-secondary mx-2 fs-4 fw-bold router-link-active"
      to="/admin/products"
      >後台產品</router-link
    >|
    <router-link
      class="link-secondary mx-2 fs-4 fw-bold router-link-active"
      to="/admin/orders"
      >後台訂單</router-link
    >
    <a href="#" @click.prevent="logout" class="link-secondary mx-2 fs-4 fw-bold"
      >登出</a
    >
  </nav>
  <hr />
  <RouterView></RouterView>
</template>

<script>
import { RouterView } from "vue-router";
export default {
  components: {
    RouterView,
  },
  methods: {
    logout() {
      document.cookie = `hexschool=; expires=;`;
      this.$router.push("/login");
    },
  },
};
</script>
