<template>
  <div class="cart">
    <div class="container">
      <section class="order">
        <h2>訂單內容</h2>
        <template v-if="orderList.length > 0">
          <ul class="orderList">
            <li
              v-for="item in orderList"
              :key="item.productId"
              class="d-flex flex-column flex-lg-row align-items-center list-unstyled orderList__item"
            >
              <img class="p-3" :src="item.imgSrc" alt="books_photo" />
              <div class="row align-items-center flex-grow-1">
                <div class="title col-12 col-lg-6 mb-3 mb-lg-0">
                  <h3>{{ item.name }}</h3>
                  <ul>
                    <li
                      v-for="(contentItem, c_idx) in item.content"
                      :key="c_idx + 1"
                    >
                      <p>{{ contentItem }}</p>
                    </li>
                  </ul>
                </div>
                <Formatter
                  data-type="NumberComma"
                  :origin-data="item.price.discount * item.qty"
                  class="col-6 col-lg-3 price"
                >
                  <template #default="propsData">
                    NT ${{ propsData.formatData }}
                  </template>
                </Formatter>
                <div class="col-6 col-lg-3 d-flex justify-content-center">
                  <client-only>
                    <minus
                      style="font-size: 24px"
                      class="pointer"
                      @click="minusQty(item.productId)"
                    />
                  </client-only>
                  <Formatter
                    data-type="NumberComma"
                    :origin-data="item.qty"
                    class="qty px-2 user-select-none"
                    style="text-align: center"
                  >
                    <template #default="propsData">
                      {{ propsData.formatData }} 件
                    </template>
                  </Formatter>
                  <client-only>
                    <plus
                      style="font-size: 24px"
                      class="pointer"
                      @click="addQty(item.productId)"
                    />
                  </client-only>
                </div>
              </div>
            </li>
          </ul>
        </template>
        <template v-else>
          <ul class="orderList">
            <li class="emptyMsg">購物車內無任何商品，請返回訂購頁</li>
          </ul>
        </template>
        <div>
          <Formatter
            data-type="NumberComma"
            :origin-data="totalPrice"
            class="total user-select-none"
          >
            <template #default="propsData">
              小計 <span> NT$ {{ propsData.formatData }}</span>
            </template>
          </Formatter>
          <div class="clearfix"></div>
        </div>
      </section>
      <div class="d-flex justify-content-end">
        <b-button variant="primary" class="ml-3" @click="$router.go(-1)"
          >返回</b-button
        >
        <b-button
          variant="secondary"
          class="ml-3"
          @click="$router.push('/cart')"
          >下一步</b-button
        >
      </div>
    </div>
    <NotifyModal
      ref="fileDeleteNote"
      ref-name="fileDeleteNote"
      title="產品刪除"
      cancel-btn-name="取消"
      confirm-btn-name="確定"
      main-content="確定要刪除此產品嗎?"
      is-confirm-show
      @confirm="removeOrderItem()"
    />
  </div>
</template>

<script>
import Cookie from 'js-cookie'
import Formatter from '@/components/Formatter.vue'
import NotifyModal from '@/components/Modal/NotifyModal.vue'
import { normalDiscount } from '@/assets/js/tool'

export default {
  components: {
    Formatter,
    NotifyModal,
  },
  layout: 'cartPage',
  middleware: ['auth'],
  data() {
    return {
      orderList: [],
      activeProductId: '',
      btnSetup: [
        { type: 'reset', btnName: '返回', variant: 'primary' },
        { type: 'submit', btnName: '訂單送出', variant: 'secondary' },
      ],
    }
  },
  head() {
    return {
      title: '購物車 - 有良冊股份有限公司',
      meta: [
        {
          // hid: 'description',
          name: 'description',
          content: '購物車 - 彙整購物車內產品',
        },
        {
          // hid: 'og:description',
          property: 'og:description',
          content: '購物車 - 彙整購物車內產品',
        },
        {
          // hid: 'og:title',
          property: 'og:title',
          content: '購物車 - 有良冊股份有限公司',
        },
        {
          // hid: 'og:image',
          property: 'og:image',
          content: '/yooooobook.jpg',
        },
        {
          // hid: 'og:url',
          property: 'og:url',
          content: 'https://www.yooooobook.com/cart',
        },
      ],
    }
  },
  computed: {
    totalPrice() {
      if (this.orderList.length > 0) {
        const priceList = this.orderList.map(
          (item) => item.price.discount * item.qty
        )
        return priceList.reduce((accumulator, currentValue) => {
          return accumulator + currentValue
        })
      } else {
        return 0
      }
    },
  },
  mounted() {
    if (Cookie.get('orderListInCart')) {
      this.orderList = JSON.parse(Cookie.get('orderListInCart'))
    }
  },
  methods: {
    minusQty(productId) {
      this.activeProductId = productId
      const productIdx = this.findProductIndex(this.orderList, productId)

      if (this.orderList[productIdx].qty - 1 > 0) {
        this.orderList[productIdx].qty--
        this.orderList[productIdx].price.discount = normalDiscount(
          this.orderList[productIdx].qty,
          this.orderList[productIdx].price.discountList
        )
        this.updateOrderListInCart()
      } else {
        this.$refs.fileDeleteNote.showModal()
      }
    },
    addQty(productId) {
      this.activeProductId = productId
      const productIdx = this.findProductIndex(this.orderList, productId)

      this.orderList[productIdx].qty++

      this.orderList[productIdx].price.discount = normalDiscount(
        this.orderList[productIdx].qty,
        this.orderList[productIdx].price.discountList
      )

      this.updateOrderListInCart()
    },
    updateOrderListInCart() {
      Cookie.set('orderListInCart', JSON.stringify(this.orderList))
    },
    removeOrderItem() {
      const productIdx = this.findProductIndex(
        this.orderList,
        this.activeProductId
      )
      this.orderList.splice(productIdx, 1)
      this.updateOrderListInCart()
    },
    findProductIndex(list, target) {
      return list.findIndex((item) => {
        return item.productId === target
      })
    },
  },
}
</script>

<style lang="scss" scoped>
@import './summary.scss';
</style>
