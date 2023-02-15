<template>
  <VueLoading v-model:active="isLoading"/>
  <div class="container">
    <table class="table mt-4">
      <thead>
        <tr>
          <th width="120">分類</th>
          <th width="180">產品名稱</th>
          <th width="120">原價</th>
          <th width="120">售價</th>
          <th width="100">是否啟用</th>
          <th width="100">照片</th>
        </tr>
      </thead>
      <tbody>
        <tr
          v-for="product in products"
          :key="product.id"
          :class="{ 'table-success': product.is_enabled }"
        >
          <td>{{ product.category }}</td>
          <td>{{ product.title }}</td>
          <td class="text-end">{{ product.origin_price }}</td>
          <td class="text-end">{{ product.price }}</td>
          <td>
            <span class="text-success" v-if="product.is_enabled">啟用</span>
            <span v-else>未啟用</span>
          </td>
          <td>
            <img :src="product.imageUrl" alt="pic" class="img-fluid" />
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
const { VITE_APP_API, VITE_APP_PATH } = import.meta.env;
export default {
  data() {
    return {
      isLoading:false,
      products: [],
    };
  },
  methods: {
    checkAdmin() {
      this.$http
        .post(`${VITE_APP_API}v2/api/user/check`)
        .then(() => {
          this.renderData();
        })
        .catch(() => {
          this.$router.push("/login");
        });
    },
    renderData() {
      this.$http
        .get(`${VITE_APP_API}v2/api/${VITE_APP_PATH}/admin/products`)
        .then((res) => {
          this.products = res.data.products;
        })
        .catch((err) => {
          alert(err.data.message);
        });
    },
  },
  mounted() {
    const token = document.cookie.replace(
      /(?:(?:^|.*;\s*)hexschool\s*=\s*([^;]*).*$)|^.*$/,
      "$1"
    );
    this.$http.defaults.headers.common["Authorization"] = token;
    this.checkAdmin();
    this.isLoading = true;
    setTimeout(() => {
      this.isLoading = false;
    }, 2000);
  },
};
</script>
