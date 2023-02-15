<template>
  <VueLoading v-model:active="isLoading" />
  <div class="container">
    <table class="table mt-4">
      <thead>
        <tr>
          <th width="100">訂單編號</th>
          <th width="120">顧客姓名</th>
          <th width="120">訂單金額</th>
          <th width="120">是否付款</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="order in orders" :key="order.id">
          <td>{{ order.id }}</td>
          <td>{{ order.user.name }}</td>
          <td>{{ order.total }}</td>
          <td>{{ order.is_paid ? "已付款" : "未付款" }}</td>
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
      isLoading: false,
      orders: [],
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
        .get(`${VITE_APP_API}v2/api/${VITE_APP_PATH}/admin/orders`)
        .then((res) => {
          this.orders = res.data.orders;
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
