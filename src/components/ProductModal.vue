<template>
  <div
    class="modal fade"
    id="productModal"
    tabindex="-1"
    role="dialog"
    aria-labelledby="exampleModalLabel"
    aria-hidden="true"
    ref="modal"
  >
    <div class="modal-dialog modal-xl" role="document">
      <div class="modal-content border-0">
        <div class="modal-header bg-dark text-white">
          <h5 class="modal-title" id="exampleModalLabel">
            <span>{{ selectProduct.title }}</span>
          </h5>
          <button
            type="button"
            class="btn-close"
            data-bs-dismiss="modal"
            aria-label="Close"
            @click="closeModal()"
          ></button>
        </div>
        <div class="modal-body">
          <div class="row">
            <div class="col-sm-6">
              <img
                class="img-fluid"
                :alt="selectProduct.title"
                :src="selectProduct.imageUrl"
              />
            </div>
            <div class="col-sm-6">
              <span class="badge bg-primary rounded-pill">{{
                selectProduct.category
              }}</span>
              <p>商品描述：<br />{{ selectProduct.description }}</p>
              <p>商品內容：<br />{{ selectProduct.content }}</p>
              <div
                class="h5"
                v-if="selectProduct.price === selectProduct.origin_price"
              >
                {{ selectProduct.price }} 元
              </div>
              <div v-else>
                <del class="h6">原價 {{ selectProduct.origin_price }} 元</del>
                <div class="h5">現在只要 {{ selectProduct.price }} 元</div>
              </div>
              <div>
                <div class="input-group">
                  <select name="" id="" class="form-select" v-model="modalQty">
                    <option :value="i" v-for="i in 20" :key="i">{{ i }}</option>
                  </select>
                  <button
                    type="button"
                    class="btn btn-primary"
                    @click="addToCart(selectProduct.id, modalQty)"
                    :disabled="
                      loadingStatus.loadingItem === selectProduct.id &&
                      loadingStatus.isLoading
                    "
                  >
                    加入購物車
                  </button>
                </div>
              </div>
            </div>
            <!-- col-sm-6 end -->
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Modal from "bootstrap/js/dist/modal";
const apiUrl = "https://vue3-course-api.hexschool.io/";
const path = "amberhh";

export default {
  props: ["id", "addToCart", "loadingStatus", "closeModal"],
  data() {
    return {
      productModal: "",
      selectProduct: "",
      modalQty: 1,
    };
  },
  methods: {
    hideModal() {
      this.modalQty = 1;
      this.productModal.hide();
    },
  },
  watch: {
    id() {
      if (this.id) {
        this.$http
          .get(`${apiUrl}v2/api/${path}/product/${this.id}`)
          .then((res) => {
            this.selectProduct = res.data.product;
            this.productModal.show();
          })
          .catch((err) => {
            alert(err.data.message);
          });
      }
    },
  },
  mounted() {
    this.productModal = new Modal(this.$refs.modal, {
      keyboard: false,
    });
  },
};
</script>
