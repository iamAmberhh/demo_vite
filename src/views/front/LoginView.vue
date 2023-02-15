<template>
  <div class="container">
    <div class="row justify-content-center">
      <h1 class="h3 mb-3 font-weight-normal text-center">請先登入</h1>
      <div class="col-8">
        <form id="form" class="form-signin">
          <div class="form-floating mb-3">
            <input
              type="email"
              class="form-control"
              id="username"
              placeholder="name@example.com"
              required
              autofocus
              v-model="user.username"
            />
            <label for="username">Email address</label>
          </div>
          <div class="form-floating">
            <input
              type="password"
              class="form-control"
              id="password"
              placeholder="Password"
              required
              v-model="user.password"
            />
            <label for="password">Password</label>
          </div>
          <button
            class="btn btn-lg btn-primary w-100 mt-3"
            @click.prevent="submit"
          >
            登入
          </button>
        </form>
      </div>
    </div>
  </div>
</template>

<script>
const { VITE_APP_API } = import.meta.env;
export default {
  data() {
    return {
      user: {
        username: "",
        password: "",
      },
    };
  },
  methods: {
    submit() {
      this.$http
        .post(`${VITE_APP_API}v2/admin/signin`, this.user)
        .then((res) => {
          const { token, expired } = res.data;
          document.cookie = `hexschool=${token}; expires=${expired};`;
          this.$router.push("/admin/products");
        })
        .catch(() => {
          alert(`登入失敗，請確認帳號密碼`);
        });
    },
  },
};
</script>
