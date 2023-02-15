<template>
  <VueLoading v-model:active="isLoading"/>
  <div class="container">
    <div class="py-4">
      <!-- 產品細節modal -->
      <product-modal
        :id="productId"
        :add-to-cart="addToCart"
        :loading-status="loadingStatus"
        :close-modal="closeModal"
        ref="productModal"
      ></product-modal>
      <!-- 產品列表 -->
      <table class="table align-middle">
        <thead>
          <tr>
            <th>圖片</th>
            <th>商品名稱</th>
            <th>價格</th>
            <th></th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="product in products" :key="product.id">
            <td style="width: 200px">
              <div
                style="
                  height: 100px;
                  background-size: cover;
                  background-position: center;
                "
                :style="{ backgroundImage: `url(${product.imageUrl})` }"
              ></div>
            </td>
            <td>
              {{ product.title }}
            </td>
            <td>
              <div class="h5" v-if="product.price === product.origin_price">
                {{ product.price }} 元
              </div>
              <div v-else>
                <del class="h6">原價 {{ product.origin_price }} 元</del>
                <div class="h5">現在只要 {{ product.price }} 元</div>
              </div>
            </td>
            <td>
              <div class="btn-group btn-group-sm">
                <button
                  type="button"
                  class="btn btn-outline-secondary"
                  @click="openModal(product.id)"
                >
                  查看更多
                </button>
                <button
                  type="button"
                  class="btn btn-outline-danger"
                  @click="addToCart(product.id)"
                  :disabled="
                    loadingStatus.loadingItem === product.id &&
                    loadingStatus.isLoading
                  "
                >
                  <i
                    class="fas fa-spinner fa-pulse"
                    v-if="loadingStatus.loadingItem === product.id"
                  ></i>
                  加到購物車
                </button>
              </div>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script>
import ProductModal from "@/components/ProductModal.vue";
const { VITE_APP_API, VITE_APP_PATH } = import.meta.env;

export default {
  data() {
    return {
      isLoading: false,
      products: [],
      productId: "",
      loadingStatus: {
        loadingItem: "",
        isLoading: false,
      },
    };
  },
  components: {
    ProductModal,
  },
  methods: {
    getProduct() {
      this.$http
        .get(`${VITE_APP_API}v2/api/${VITE_APP_PATH}/products/all`)
        .then((res) => {
          this.products = res.data.products;
        })
        .catch((err) => {
          alert(err.data.message);
        });
    },
    openModal(id) {
      this.productId = id;
    },
    closeModal() {
      (this.productId = ""), this.$refs.productModal.hideModal();
    },
    clearLoadingStatus() {
      this.loadingStatus.loadingItem = "";
      this.loadingStatus.isLoading = false;
    },
    addToCart(product_id, qty = 1) {
      this.loadingStatus.loadingItem = product_id;
      this.loadingStatus.isLoading = true;
      const data = {
        product_id,
        qty,
      };
      this.$http
        .post(`${VITE_APP_API}v2/api/${VITE_APP_PATH}/cart`, { data })
        .then((res) => {
          alert(res.data.message);
          this.productId = "";
          this.$refs.productModal.hideModal();
          this.clearLoadingStatus();
        })
        .catch((err) => {
          alert(err.data.message);
        });
    },
  },
  mounted() {
    this.getProduct();
    this.isLoading = true;
    setTimeout(() => {
      this.isLoading = false;
    }, 2000);
  },
};
</script>
