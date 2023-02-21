<template>
  這是購物車
   <div class="text-end">
      <button class="btn btn-outline-danger" type="button" @click="deleteAllCarts">清空購物車</button>
    </div>
    <table class="table align-middle">
      <thead>
        <tr>
          <th></th>
          <th>品名</th>
          <th style="width: 150px">數量/單位</th>
          <th>單價</th>
        </tr>
      </thead>
      <tbody>
        <template v-if="cart.carts">
          <tr v-for="item in cart.carts" :key="item.id">
            <td>
              <button type="button" class="btn btn-outline-danger btn-sm" @click="removeCartItem(item.id)"
                :disabled="loadingStatus.loadingItem === item.id">
                <i class="fas fa-spinner fa-pulse" v-if="loadingStatus.loadingItem === item.id"></i>
                x
              </button>
            </td>
            <td>
              {{ item.product.title }}
              <div class="text-success" v-if="item.coupon">
                已套用優惠券
              </div>
            </td>
            <td>
              <div class="input-group input-group-sm">
                <div class="input-group mb-3">
                  <input v-model.number="item.qty" @blur="updateCart(item)"
                    :disabled="loadingStatus.loadingItem === item.id" min="1" type="number" class="form-control">
                  <span class="input-group-text" id="basic-addon2">{{ item.product.unit }}</span>
                </div>
              </div>
            </td>
            <td class="text-end">
              <small v-if="cart.final_total !== cart.total" class="text-success">折扣價：</small>
              {{ item.final_total }}
            </td>
          </tr>
        </template>
      </tbody>
      <tfoot>
        <tr>
          <td colspan="3" class="text-end">總計</td>
          <td class="text-end">{{ cart.total }}</td>
        </tr>
        <tr v-if="cart.final_total !== cart.total">
          <td colspan="3" class="text-end text-success">折扣價</td>
          <td class="text-end text-success">{{ cart.final_total }}</td>
        </tr>
      </tfoot>
    </table>

</template>

<script>
const { VITE_APP_URL, VITE_PATH } = import.meta.env
export default {
  data () {
    return {
      loadingStatus: {
        loadingItem: ''
      },
      products: [],
      product: {},
      form: {
        user: {
          name: '',
          email: '',
          tel: '',
          address: ''
        },
        message: ''
      },
      cart: {}
    }
  },
  methods: {
    getProduct (id) {
      const url = `${VITE_APP_URL}/api/${VITE_PATH}/product/${id}`
      this.loadingStatus.loadingItem = id
      this.$http.get(url).then((response) => {
        this.loadingStatus.loadingItem = ''
        this.product = response.data.product
        this.$refs.userProductModal.openModal()
      }).catch((err) => {
        alert(err.response.data.message)
      })
    },
    updateCart (data) {
      this.loadingStatus.loadingItem = data.id
      const url = `${VITE_APP_URL}/api/${VITE_PATH}/cart/${data.id}`
      const cart = {
        product_id: data.product_id,
        qty: data.qty
      }
      this.$http.put(url, { data: cart }).then((response) => {
        alert(response.data.message)
        this.loadingStatus.loadingItem = ''
        this.getCart()
      }).catch((err) => {
        alert(err.response.data.message)
        this.loadingStatus.loadingItem = ''
      })
    },
    deleteAllCarts () {
      const url = `${VITE_APP_URL}/api/${VITE_PATH}/carts`
      this.$http.delete(url).then((response) => {
        alert(response.data.message)
        this.getCart()
      }).catch((err) => {
        alert(err.response.data.message)
      })
    },
    getCart () {
      const url = `${VITE_APP_URL}/api/${VITE_PATH}/cart`
      this.$http.get(url).then((response) => {
        this.cart = response.data.data
      }).catch((err) => {
        alert(err.response.data.message)
      })
    },
    removeCartItem (id) {
      const url = `${VITE_APP_URL}/api/${VITE_PATH}/cart/${id}`
      this.loadingStatus.loadingItem = id
      this.$http.delete(url).then((response) => {
        alert(response.data.message)
        this.loadingStatus.loadingItem = ''
        this.getCart()
      }).catch((err) => {
        alert(err.response.data.message)
      })
    },
    createOrder () {
      const url = `${VITE_APP_URL}/api/${VITE_PATH}/order`
      const order = this.form
      this.$http.post(url, { data: order }).then((response) => {
        alert(response.data.message)
        this.$refs.form.resetForm()
        this.getCart()
      }).catch((err) => {
        alert(err.response.data.message)
      })
    }

  },
  mounted () {
    this.getCart()
  }
}
</script>
