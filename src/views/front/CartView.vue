<template>
  <VueLoading v-model:active="isLoading"/>
  <div class="container">
    <div class="text-center" v-if="!cartStatus">
      <p>購物車內還沒有商品，快去選購吧！</p>
    </div>
    <div v-else>
      <div class="text-end">
        <button
          class="btn btn-outline-danger"
          type="button"
          :disabled="!cartStatus"
          @click="removeAllCart()"
        >
          清空購物車
        </button>
      </div>
      <table class="table align-middle">
        <thead>
          <tr>
            <th></th>
            <th>品名</th>
            <th style="width: 150px">數量/單位</th>
            <th>單價</th>
            <th>小計</th>
          </tr>
        </thead>
        <tbody>
          <template v-if="cart.carts">
            <tr v-for="item in cart.carts" :key="item.id">
              <td>
                <button
                  type="button"
                  class="btn btn-outline-danger btn-sm"
                  @click="removeCartItem(item.id)"
                  :disabled="
                    loadingStatus.loadingItem === item.id &&
                    loadingStatus.isLoading
                  "
                >
                  <i
                    class="fas fa-spinner fa-pulse"
                    v-if="loadingStatus.loadingItem === item.id"
                  ></i>
                  x
                </button>
              </td>
              <td>
                {{ item.product.title }}
                <div class="text-success">已套用優惠券</div>
              </td>
              <td>
                <div class="input-group input-group-sm">
                  <div class="input-group mb-3">
                    <select
                      name=""
                      id=""
                      class="form-select"
                      v-model="item.qty"
                      @change="changeQty(item.id, item.product_id, item.qty)"
                    >
                      <option :value="i" v-for="i in 20" :key="i">
                        {{ i }}
                      </option>
                    </select>
                  </div>
                </div>
              </td>
              <td class="text-end">
                {{ item.product.price }}
              </td>
              <td class="text-end">
                {{ item.final_total }}
              </td>
            </tr>
          </template>
        </tbody>
        <tfoot>
          <tr>
            <td colspan="4" class="text-end">總計</td>
            <td class="text-end">{{ cart.final_total }}</td>
          </tr>
        </tfoot>
      </table>
    </div>

    <hr class="my-2" />
    <div class="my-5 row justify-content-center">
      <VForm ref="form" class="col-md-6" v-slot="{ errors }" @submit="onSubmit">
        <div class="mb-3">
          <label for="email" class="form-label"
            >Email<span class="ms-1 text-danger">*</span></label
          >
          <VField
            id="email"
            name="email"
            type="email"
            class="form-control"
            :class="{ 'is-invalid': errors['email'] }"
            placeholder="請輸入 Email"
            rules="email|required"
            v-model="user.email"
          ></VField>
          <ErrorMessage name="email" class="invalid-feedback"></ErrorMessage>
        </div>

        <div class="mb-3">
          <label for="name" class="form-label"
            >收件人姓名<span class="ms-1 text-danger">*</span></label
          >
          <VField
            id="name"
            name="姓名"
            type="text"
            class="form-control"
            :class="{ 'is-invalid': errors['姓名'] }"
            placeholder="請輸入姓名"
            rules="required"
            v-model="user.name"
          ></VField>
          <ErrorMessage name="姓名" class="invalid-feedback"></ErrorMessage>
        </div>

        <div class="mb-3">
          <label for="tel" class="form-label"
            >收件人電話<span class="ms-1 text-danger">*</span></label
          >
          <VField
            id="tel"
            name="電話"
            type="tel"
            class="form-control"
            :class="{ 'is-invalid': errors['電話'] }"
            placeholder="請輸入電話"
            :rules="isPhone"
            v-model="user.tel"
          ></VField>
          <ErrorMessage name="電話" class="invalid-feedback"></ErrorMessage>
        </div>

        <div class="mb-3">
          <label for="address" class="form-label"
            >收件人地址<span class="ms-1 text-danger">*</span></label
          >
          <VField
            id="address"
            name="地址"
            type="text"
            class="form-control"
            :class="{ 'is-invalid': errors['地址'] }"
            placeholder="請輸入地址"
            rules="required"
            v-model="user.address"
          ></VField>
          <ErrorMessage name="地址" class="invalid-feedback"></ErrorMessage>
        </div>

        <div class="mb-3">
          <label for="message" class="form-label">留言</label>
          <textarea
            id="message"
            class="form-control"
            cols="30"
            rows="10"
            v-model="message"
          ></textarea>
        </div>
        <div class="text-end">
          <button type="submit" class="btn btn-danger">送出訂單</button>
        </div>
      </VForm>
    </div>
  </div>
</template>

<script>
import { ErrorMessage } from "vee-validate";
const { VITE_APP_API, VITE_APP_PATH } = import.meta.env;

export default {
  data() {
    return {
      isLoading: false,
      cart: [],
      cartStatus: false,
      loadingStatus: {
        loadingItem: "",
        isLoading: false,
      },
      user: {
        email: "",
        name: "",
        tel: "",
        address: "",
      },
      message: "",
    };
  },
  methods: {
    getCartList() {
      this.$http
        .get(`${VITE_APP_API}v2/api/${VITE_APP_PATH}/cart`)
        .then((res) => {
          this.cart = res.data.data;
          if (this.cart.carts.length === 0) {
            this.cartStatus = false;
          } else {
            this.cartStatus = true;
          }
        })
        .catch((err) => {
          alert(err.data.message);
        });
    },
    clearLoadingStatus() {
      this.loadingStatus.loadingItem = "";
      this.loadingStatus.isLoading = false;
    },
    changeQty(id, product_id, qty) {
      const data = {
        product_id,
        qty,
      };
      this.$http
        .put(`${VITE_APP_API}v2/api/${VITE_APP_PATH}/cart/${id}`, { data })
        .then(() => {
          alert("已更新產品數量");
          this.getCartList();
        })
        .catch((err) => {
          alert(err.data.message);
        });
    },
    removeCartItem(id) {
      this.loadingStatus.loadingItem = id;
      this.loadingStatus.isLoading = true;
      this.$http
        .delete(`${VITE_APP_API}v2/api/${VITE_APP_PATH}/cart/${id}`)
        .then(() => {
          alert(`已刪除購物車品項`);
          this.getCartList();
          this.clearLoadingStatus();
        })
        .catch((err) => {
          alert(err.data.message);
        });
    },
    removeAllCart() {
      this.$http
        .delete(`${VITE_APP_API}v2/api/${VITE_APP_PATH}/carts`)
        .then(() => {
          alert(`已清空購物車`);
          this.getCartList();
        })
        .catch((err) => {
          alert(err.data.message);
        });
    },
    isPhone(value) {
      const phoneNumber = /^(09)[0-9]{8}$/;
      return phoneNumber.test(value) ? true : "需要正確的手機號碼";
    },
    onSubmit() {
      const data = {
        user: this.user,
        message: this.message,
      };
      if (this.cart.carts.length === 0) {
        alert("購物車內還沒有商品唷～");
        return;
      }
      this.$http
        .post(`${VITE_APP_API}v2/api/${VITE_APP_PATH}/order`, { data })
        .then((res) => {
          alert(res.data.message);
          this.$refs.form.resetForm();
          this.message = "";
          this.getCartList();
        })
        .catch((err) => {
          alert(err.data.message);
        });
    },
  },
  mounted() {
    this.getCartList();
    this.isLoading = true;
    setTimeout(() => {
      this.isLoading = false;
    }, 2000);
  },
  components: { ErrorMessage },
};
</script>
