<template>
  <div class="cart">
    <div class="container">
      <section class="order">
        <h2>訂單內容</h2>

        <div class="orderList">
          <ul>
            <li
              v-for="(item, idx) in orderList"
              :key="idx + 1"
              class="orderList__item"
            >
              <img :src="item.imgSrc" alt="books_photo" />
              <div class="right">
                <div class="title">
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
                  class="price"
                >
                  <template #default="propsData">
                    NT ${{ propsData.formatData }}
                  </template>
                </Formatter>
                <Formatter
                  data-type="NumberComma"
                  :origin-data="item.qty"
                  class="qty"
                >
                  <template #default="propsData">
                    {{ propsData.formatData }}件
                  </template>
                </Formatter>
              </div>
            </li>
          </ul>
          <p v-show="orderList.length === 0" class="emptyMsg">
            購物車內無任何商品，請返回訂購頁
          </p>
        </div>

        <div class="d-flex flex-column align-items-end">
          <Formatter
            data-type="NumberComma"
            :origin-data="totalPrice"
            class="total"
          >
            <template #default="propsData">
              小計 <span> NT$ {{ propsData.formatData }}</span>
            </template>
          </Formatter>
          <div class="clearfix"></div>
        </div>
      </section>

      <section class="buyerInfo">
        <h2>訂購人資訊</h2>

        <OrderForm
          ref="orderForm"
          :form-data="inputValue"
          :input-setup="inputSetup"
          :btn-setup="btnSetup"
          @submitEvent="sendOrder"
          @resetEvent="$router.back()"
          @toastEvent="formValidateError"
          @orderQtyError="orderQtyError"
        />
      </section>
    </div>
    <Toaster :title="toastSet.title" :content="toastSet.content" />
  </div>
</template>

<script>
import Cookie from 'js-cookie'
import { initOrderInfo } from '@/assets/js/initData'
import OrderForm from '@/components/Form/OrderForm.vue'
import Formatter from '@/components/Formatter.vue'
import Toaster from '@/components/Toaster/Toaster.vue'

export default {
  components: {
    OrderForm,
    Formatter,
    Toaster
  },
  layout: 'cartPage',
  middleware: ['auth'],
  data() {
    return {
      orderList: [],
      orderInfo: {},
      inputSetup: [
        {
          title: '收件人姓名',
          type: 'text',
          placeholder: '請輸入姓名',
          attrName: 'recipient',
          required: true,
          invalidFeedback: '請輸入收件人姓名',
          id: 'main-recipient'
        },
        {
          title: '收件人地址',
          type: 'text',
          placeholder: '請輸入地址',
          attrName: 'address',
          required: true,
          invalidFeedback: '請輸入地址',
          id: 'main-address'
        },
        {
          title: 'Email',
          type: 'email',
          placeholder: 'example@gmail.com',
          attrName: 'email',
          required: true,
          invalidFeedback: '請確認email格式',
          id: 'main-email'
        },
        {
          title: '聯絡電話',
          type: 'tel',
          placeholder: '請輸入電話號碼',
          attrName: 'phone',
          required: true,
          invalidFeedback: '請輸入電話號碼',
          id: 'main-phone'
        },
        {
          title: '帳戶後5碼',
          type: 'text',
          placeholder: '請輸入金融帳戶後5碼',
          attrName: 'account',
          required: true,
          invalidFeedback: '請確認輸入之數字',
          id: 'main-account'
        },
        {
          title: '發票買受人',
          type: 'text',
          placeholder: '請輸入發票買受人',
          attrName: 'buyer',
          required: true,
          invalidFeedback: '請輸入發票買受人',
          id: 'main-buyer'
        },
        {
          title: '統一編號',
          type: 'text',
          placeholder: '請輸入統一編號(選填)',
          attrName: 'taxId',
          required: false,
          invalidFeedback: '請輸入8碼統一編號',
          id: 'main-taxId'
        },
        {
          title: '訂單備註',
          type: 'text',
          placeholder: '請輸入備註內容',
          attrName: 'buyerRemark',
          required: false,
          invalidFeedback: '請輸入備註內容',
          id: 'main-buyerRemark'
        }
      ],
      btnSetup: [
        { type: 'reset', btnName: '返回', variant: 'primary' },
        { type: 'submit', btnName: '訂單送出', variant: 'secondary' }
      ],
      inputValue: {
        recipient: '',
        address: '',
        phone: '',
        email: '',
        account: '',
        buyer: '',
        taxId: '',
        buyerRemark: ''
      },
      toastSet: {
        title: '',
        content: ''
      },
      stock: null
    }
  },
  head() {
    return {
      title: '購物車 - 有良冊股份有限公司',
      meta: [
        {
          // hid: 'description',
          name: 'description',
          content: '購物車 - 有良冊股份有限公司'
        },
        {
          // hid: 'og:description',
          property: 'og:description',
          content: '購物車 - 有良冊股份有限公司'
        },
        {
          // hid: 'og:title',
          property: 'og:title',
          content: '購物車 - 有良冊股份有限公司'
        },
        {
          // hid: 'og:image',
          property: 'og:image',
          content: '/yooooobook.jpg'
        },
        {
          // hid: 'og:url',
          property: 'og:url',
          content: 'https://www.yooooobook.com/cart'
        }
      ]
    }
  },
  computed: {
    totalPrice() {
      if (this.orderList.length > 0) {
        const priceList = this.orderList.map(
          (item) => item.price.discount * item.qty
        )
        const allProductPrice = priceList.reduce(
          (accumulator, currentValue) => {
            return accumulator + currentValue
          }
        )

        return allProductPrice
      } else {
        return 0
      }
    }
  },
  created() {
    const orderListInCart = Cookie.get('orderListInCart')
    if (orderListInCart) {
      this.orderList = JSON.parse(orderListInCart)
    } else {
      this.orderInfo = initOrderInfo()
    }
  },
  methods: {
    async sendOrder(buyerInfo) {
      if (this.orderList.length === 0) return
      if (!process.client) return

      this.setOrderInfo(buyerInfo)

      const orderInfo = this.orderInfo
      const orderId = orderInfo.orderId
      const uid = this.$store.state.userUid
      const idToken = Cookie.get('id_token')

      const isStockUpdated = await this.updateStock(idToken)
      if (isStockUpdated) {
        this.$nuxt.$loading.start()
        await this.patchOrderInfoPromise(uid, orderId, idToken, orderInfo)
        // 發送訂單確認信
        await this.sendOrderConfirmMailPromise(orderInfo)
        // 訂單資訊存入vuex
        this.$store.commit('seUsertOrder', orderInfo)

        // 清除Cookie
        Cookie.remove('orderListInCart')
        this.$nuxt.$loading.finish()
        this.$router.push('/cart/success/' + orderInfo.orderId)
      }
    },
    formatTimeDate(s) {
      return s < 10 ? '0' + s : s
    },
    oderDateCreater() {
      const now = new Date()
      const _year = now.getFullYear()
      const _month = this.formatTimeDate(now.getMonth() + 1)
      const _day = this.formatTimeDate(now.getDate())
      const _hour = this.formatTimeDate(now.getHours())
      const _minutes = this.formatTimeDate(now.getMinutes())

      const orderDate = `${_year.toString()}/${_month.toString()}/${_day} ${_hour}:${_minutes}`
      return orderDate
    },
    orderIdCreater() {
      const now = new Date()
      const _year = now.getFullYear()
      const _month = this.formatTimeDate(now.getMonth() + 1)
      const _day = this.formatTimeDate(now.getDate())
      const _hour = this.formatTimeDate(now.getHours())
      const _minutes = this.formatTimeDate(now.getMinutes())
      const _seconds = this.formatTimeDate(now.getSeconds())

      const orderCode =
        _year.toString().slice(2, 4) +
        _month.toString() +
        _day +
        _hour +
        _minutes +
        _seconds +
        Math.round(Math.random() * (999 - 100) + 100).toString()

      return orderCode
    },
    setOrderInfo(buyerInfo) {
      const orderId = this.orderIdCreater()
      const oderDate = this.oderDateCreater()

      const orderList = this.orderList.map((item) => {
        return {
          productId: item.productId,
          productName: item.name,
          qty: item.qty,
          unitPrice: item.price.discount,
          promote: item.price.promote,
          discountList: item.price.discountList,
          totalPrice: item.price.discount * item.qty
        }
      })

      const orderInfo = initOrderInfo()

      // 更新要送出的資料
      orderInfo.buyer = buyerInfo.buyer
      orderInfo.receiver.name = buyerInfo.recipient
      orderInfo.receiver.address = buyerInfo.address
      orderInfo.email = buyerInfo.email
      orderInfo.phone = buyerInfo.phone
      orderInfo.taxId = buyerInfo.taxId
      orderInfo.bankAccountNo = buyerInfo.account
      orderInfo.buyerRemark = buyerInfo.buyerRemark
      orderInfo.orderId = orderId
      orderInfo.orderList = orderList
      orderInfo.totalPrice = this.totalPrice
      orderInfo.oderDate = oderDate
      orderInfo.status = '1'
      orderInfo.isClosed = false

      this.orderInfo = orderInfo
    },
    sendOrderConfirmMailPromise(orderInfo) {
      try {
        return this.$axios({
          method: 'post',
          baseURL: process.env.WEB_URL,
          url: '/mail/orderConfirm',
          data: orderInfo
        })
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log('發送訂單信件失敗', e)
      }
    },
    getStockPromise(productId) {
      try {
        return this.$api.stock.getStock(productId)
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log(`取得${productId}庫存失敗`, e)
      }
    },
    async updateStock(idToken) {
      let isOrderListSotckAvaiable = false
      let isOrderListSotckUpdated = false

      // 初始化資料
      const orders = this.orderList.map((order) => {
        return {
          productId: order.productId,
          orderQty: order.qty,
          stock: null,
          isAvaiable: false,
          isUpdated: false
        }
      })

      // 取得各產品庫存 > 確認庫存 > 紀錄庫存
      for (let i = 0; i < orders.length; i++) {
        const { productId, orderQty } = orders[i]
        const { data } = await this.getStockPromise(productId)
        const stock = data.qty

        if (stock >= orderQty) {
          orders[i].stock = stock
          orders[i].isAvaiable = true
        } else {
          orders[i].stock = stock
          const productName = orders[i].name
          this.openToast('庫存不足', `${productName} 庫存不足`)
        }
      }

      isOrderListSotckAvaiable = !orders
        .map((order) => order.isAvaiable)
        .includes(false)

      // 更新庫存
      if (isOrderListSotckAvaiable) {
        for (let i = 0; i < orders.length; i++) {
          if (orders[i].isUpdated) return

          const { productId, orderQty, stock } = orders[i]
          const updatedStock = stock - orderQty
          const data = { qty: updatedStock }
          try {
            await this.$api.stock.patchStock(productId, data, idToken)
            orders[i].isUpdated = true
          } catch (e) {
            const productName = orders[i].name
            // eslint-disable-next-line no-console
            console.log(`${productName} 庫存更新失敗`, e)
          }
        }
      }

      return new Promise((resolve, reject) => {
        isOrderListSotckUpdated = !orders
          .map((order) => order.isUpdated)
          .includes(false)
        resolve(isOrderListSotckUpdated)
      })
    },
    patchOrderInfoPromise(uid, orderId, idToken, orderInfo) {
      try {
        return this.$api.order.patchOrderInfo(uid, orderId, idToken, orderInfo)
      } catch (e) {
        // eslint-disable-next-line no-console
        console.log(e)
      }
    },
    openToast(title, content) {
      this.toastSet.title = title
      this.toastSet.content = content
      this.$bvToast.show('my-toast')
    },
    orderQtyError(title, content) {
      this.openToast(title, content)
    },
    formValidateError(title, content) {
      this.openToast(title, content)
    }
  }
}
</script>

<style lang="scss" scoped>
@import './cart.scss';
</style>
