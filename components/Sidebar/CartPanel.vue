<template>
  <div class="cart">
    <div v-b-toggle.sidebar-footer>
      <client-only>
        <Cart class="cartIcon" />
      </client-only>
      <span class="cartCounter pb-1">{{ cartQty }}</span>
    </div>

    <b-sidebar
      id="sidebar-footer"
      title="購物車"
      aria-label="cart"
      :backdrop="true"
      right
      shadow
      no-close-on-backdrop
      header-class="cart-header"
    >
      <template #footer="{ hide }">
        <div
          class="d-flex justify-content-end align-items-center bg-dark text-light px-3 py-2"
        >
          <button type="button" class="btn btn-primary mx-2" @click="hide">
            關閉
          </button>
          <button type="button" class="btn btn-success" @click="toCartPage">
            結帳
          </button>
        </div>
      </template>
      <div class="px-3 py-4">
        <ul>
          <li
            v-for="order in orderListCart"
            :key="order.name"
            class="d-flex justify-content-center align-items-center pb-3 mb-3 border-bottom border-primary"
          >
            <img
              style="width: 100px; height: 100px"
              class="mr-3"
              :src="order.imgSrc"
              alt="product-img"
            />

            <div class="d-flex flex-column">
              <p>{{ order.name }}</p>
              <div class="d-flex justify-content-between align-items-center">
                <div class="d-flex justify-content-center align-items-center">
                  <client-only>
                    <minus
                      style="font-size: 24px"
                      class="pointer"
                      @click="minusQty(order.productId)"
                    />
                  </client-only>
                  <p class="mb-0 mx-3">{{ order.orderQty }}</p>
                  <client-only>
                    <plus
                      style="font-size: 24px"
                      class="pointer"
                      @click="addQty(order.productId)"
                    />
                  </client-only>
                </div>
                <Formatter
                  data-type="NumberComma"
                  :origin-data="order.totalPrice"
                >
                  <template #default="propsData">
                    ${{ propsData.formatData }}
                  </template>
                </Formatter>
              </div>
            </div>
          </li>
        </ul>
      </div>
    </b-sidebar>
  </div>
</template>

<script>
import { normalDiscount } from '@/assets/js/tool'

export default {
  name: 'Sidebar',
  props: {
    title: {
      type: String,
      default: 'Sidebar'
    }
  },
  data() {
    return {
      activeProductId: ''
    }
  },
  computed: {
    cartQty() {
      return this.orderList.length
    },
    orderListCart: {
      get() {
        const orderList = JSON.parse(JSON.stringify(this.orderList))
        const tempOrders = orderList.map((order) => {
          const qty = order.qty
          const discountList = order.price.discountList
          const unitPrice = normalDiscount(qty, discountList)

          return {
            name: order.name,
            productId: order.productId,
            imgSrc: order.imgSrc,
            unitPrice,
            totalPrice: unitPrice * qty,
            orderQty: qty
          }
        })

        return tempOrders
      },
      set(orderList) {
        this.orderList = orderList
      }
    },
    orderList() {
      return this.$store.state.orderListCart
    }
  },
  created() {},
  methods: {
    minusQty(productId) {
      const orderList = JSON.parse(JSON.stringify(this.orderList))
      const productIdx = this.findProductIndex(orderList, productId)
      this.activeProductId = productId

      if (orderList[productIdx].qty - 1 > 0) {
        orderList[productIdx].qty--
        orderList[productIdx].price.discount = normalDiscount(
          orderList[productIdx].qty,
          orderList[productIdx].price.discountList
        )

        this.$store.dispatch('updateOrderList', orderList)
      } else {
        const h = this.$createElement
        const okTitle = h('span', { class: ['px-5', 'm-0'] }, '確定')
        const cancelTitle = h('span', { class: ['px-5', 'm-0'] }, '取消')
        this.$bvModal
          .msgBoxConfirm('確定要刪除此產品嗎?', {
            title: '產品刪除',
            okTitle,
            okVariant: 'danger',
            cancelTitle,
            hideHeaderClose: true,
            centered: true,
            footerClass: 'mx-auto border-0',
            buttonSize: 'md',
            bodyClass: 'text-center pt-5 pb-3'
          })
          .then((res) => {
            if (res) {
              orderList.splice(productIdx, 1)
              this.$store.dispatch('updateOrderList', orderList)
            }
          })
      }
    },
    addQty(productId) {
      const orderList = JSON.parse(JSON.stringify(this.orderList))
      const productIdx = this.findProductIndex(orderList, productId)
      const orderQty = orderList[productIdx].qty
      const stock = orderList[productIdx].stock

      if (orderQty + 1 <= stock) {
        orderList[productIdx].qty++
        orderList[productIdx].price.discount = normalDiscount(
          orderList[productIdx].qty,
          orderList[productIdx].price.discountList
        )

        this.$store.dispatch('updateOrderList', orderList)
      }
    },
    findProductIndex(list, target) {
      return list.findIndex((item) => {
        return item.productId === target
      })
    },
    toCartPage() {
      if (this.orderList.length > 0) {
        this.$router.push('/cart')
      }
    }
  }
}
</script>

<style lang="scss">
#sidebar-footer .cart-header {
  flex-direction: row-reverse;
  justify-content: space-between;
  background-color: #003756;
  color: #fff;
  .close {
    margin: 0;
    color: #fff !important;
  }
}

.cart {
  position: relative;
  transition: 0.5s;
  padding: 6px;
  &:hover {
    cursor: pointer;
    color: #003756;
  }

  .cartIcon {
    font-size: 32px;
  }
  .cartCounter {
    position: absolute;
    top: 50%;
    right: -10px;
    display: flex;
    justify-content: center;
    align-items: center;
    width: 24px;
    height: 24px;
    background-color: red;
    border-radius: 50%;
    color: #fff;
  }
}

.pointer {
  cursor: pointer;
}
</style>
