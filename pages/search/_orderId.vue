<template>
  <div class="container py-5">
    <div class="row">
      <div
        class="col-12 d-flex justify-content-between align-items-center mb-3"
      >
        <h3 class="mb-0">訂單摘要</h3>
        <span
          class="text-decoration-underline"
          style="cursor: pointer"
          @click="$router.go(-1)"
          >回到訂單列表</span
        >
      </div>
    </div>
    <div class="row border-md border-top py-2 mb-3 mx-1 mx-sm-0">
      <div class="col-12">
        <div class="row mb-2">
          <div class="col-4 col-md-2">
            <label class="mb-0 font-weight-bold">訂單信息</label>
          </div>
          <div class="col-8 col-md-10">
            <Formatter
              class="mb-0 font-weight-bold text-danger"
              :origin-data="orderInfo.status"
              data-type="orderStatus"
            >
              <template #default="propsData">
                {{ propsData.formatData }}
              </template>
            </Formatter>
          </div>
        </div>
      </div>
      <div class="col-12">
        <div class="row mb-2">
          <div class="col-4 col-md-2">
            <label class="mb-0">訂單號碼： </label>
          </div>
          <div class="col-8 col-md-10">
            <p class="mb-0">{{ orderInfo.orderId }}</p>
          </div>
        </div>
      </div>
      <div class="col-12">
        <div class="row mb-2">
          <div class="col-4 col-md-2">
            <label class="mb-0">訂單日期：</label>
          </div>
          <div class="col-8 col-md-10">
            <p class="mb-0">{{ orderInfo.oderDate }}</p>
          </div>
        </div>
      </div>
      <div class="col-12 col-lg-8">
        <label class="mb-0 mb-2">訂單明細：</label>

        <b-table
          :fields="fields"
          :items="orderItems"
          outlined
          bordered
          small
          stacked="md"
          head-variant="dark"
          class="mb-2"
        >
          <template #cell(index)="data">
            {{ data.index + 1 }}
          </template>

          <template #cell(orderQty)="data">
            <Formatter
              :origin-data="
                data.item.orderQty === null ? 0 : data.item.orderQty
              "
              data-type="NumberComma"
              class="mb-0"
            >
              <template #default="propsData">
                {{ propsData.formatData }}
              </template>
            </Formatter>
          </template>

          <template #cell(unitPrice)="data">
            <Formatter
              :origin-data="
                data.item.unitPrice === null ? 0 : data.item.unitPrice
              "
              data-type="NumberComma"
              class="mb-0"
            >
              <template #default="propsData">
                {{ propsData.formatData }}
              </template>
            </Formatter>
          </template>

          <template #cell(price)="data">
            <Formatter
              :origin-data="data.item.price === null ? 0 : data.item.price"
              data-type="NumberComma"
              class="mb-0"
            >
              <template #default="propsData">
                {{ propsData.formatData }}
              </template>
            </Formatter>
          </template>
        </b-table>
        <p class="text-right mb-0">合計：{{ totalPrice }} 元</p>
      </div>
    </div>
    <div class="row">
      <div
        class="col-12 d-flex justify-content-between align-items-center mb-3"
      >
        <h3 class="mb-0">訂購人資訊</h3>
      </div>
    </div>
    <div class="row border-md border-top py-2 mb-3 mx-1 mx-sm-0">
      <div class="col-12">
        <div class="row mb-2">
          <div class="col-4 col-md-2">
            <label class="mb-0">發票抬頭</label>
          </div>
          <div class="col-8 col-md-10">
            <p class="mb-0">
              {{ orderInfo.buyer }}
            </p>
          </div>
        </div>
      </div>
      <div class="col-12">
        <div class="row mb-2">
          <div class="col-4 col-md-2">
            <label class="mb-0">統一編號</label>
          </div>
          <div class="col-8 col-md-10">
            <p class="mb-0">
              {{ orderInfo.taxId }}
            </p>
          </div>
        </div>
      </div>
      <div class="col-12">
        <div class="row mb-2">
          <div class="col-4 col-md-2">
            <label class="mb-0">收件人</label>
          </div>
          <div class="col-8 col-md-10">
            <p class="mb-0">
              {{ orderInfo.receiver.name }}
            </p>
          </div>
        </div>
      </div>
      <div class="col-12">
        <div class="row mb-2">
          <div class="col-4 col-md-2">
            <label class="mb-0">聯絡電話</label>
          </div>
          <div class="col-8 col-md-10">
            <p class="mb-0">
              {{ orderInfo.phone }}
            </p>
          </div>
        </div>
      </div>
      <div class="col-12">
        <div class="row mb-2">
          <div class="col-4 col-md-2">
            <label class="mb-0">帳戶後5碼</label>
          </div>
          <div class="col-8 col-md-10">
            <p class="mb-0">
              {{ orderInfo.bankAccountNo }}
            </p>
          </div>
        </div>
      </div>
      <div class="col-12">
        <div class="row mb-2">
          <div class="col-4 col-md-2">
            <label class="mb-0">備註</label>
          </div>
          <div class="col-8 col-md-10">
            <pre class="mb-0" style="white-space: pre-line">
              {{ orderInfo.buyerRemark }}
            </pre>
          </div>
        </div>
      </div>
      <div class="col-12">
        <div class="row mb-2">
          <div class="col-4 col-md-2">
            <label class="mb-0 font-weight-bold mt-3">物流信息</label>
          </div>
          <div class="col-8 col-md-10">
            <a
              :style="{
                display:
                  orderInfo.delivery.trackingUrl === '' ? 'none' : 'block'
              }"
              :href="orderInfo.delivery.trackingUrl"
              target="_blank"
              class="mb-0 text-danger font-weight-bold text-decoration-underline"
              >物流單號：{{ orderInfo.delivery.trackingNo }}</a
            >
          </div>
        </div>
      </div>
      <div class="col-12">
        <div class="row mb-2">
          <div class="col-4 col-md-2">
            <label class="mb-0">收貨地址：</label>
          </div>
          <div class="col-8 col-md-10">
            <p class="mb-0">{{ orderInfo.receiver.address }}</p>
          </div>
        </div>
      </div>
      <div class="col-12">
        <div class="row mb-2">
          <div class="col-4 col-md-2">
            <label class="mb-0">寄送套數：</label>
          </div>
          <div class="col-8 col-md-10">
            <p class="mb-0">{{ orderInfo.mainOrderQty }}</p>
          </div>
        </div>
      </div>
      <div class="col-12">
        <div class="row mb-2">
          <div class="col-4 col-md-2">
            <label class="mb-0">送貨方式：</label>
          </div>
          <div class="col-8 col-md-10">
            <Formatter
              data-type="deliveryCompany"
              :origin-data="orderInfo.delivery.company"
              class="mb-0"
            >
              <template #default="propsData">
                {{ propsData.formatData }}
              </template>
            </Formatter>
          </div>
        </div>
      </div>
    </div>

    <div v-if="orderInfo.deliveryList" class="row">
      <div
        class="col-12 d-flex justify-content-between align-items-center mb-3"
      >
        <h3 class="mb-0">其他收件者</h3>
      </div>
    </div>
    <div v-for="(deliveryItem, idx) in orderInfo.deliveryList" :key="idx">
      <div class="row border-md border-top py-2 mb-3 mx-1 mx-sm-0">
        <div class="col-12">
          <div class="row mb-2">
            <div class="col-4 col-md-2">
              <label class="mb-0">發票抬頭</label>
            </div>
            <div class="col-8 col-md-10">
              <p class="mb-0">
                {{ deliveryItem.buyer }}
              </p>
            </div>
          </div>
        </div>
        <div class="col-12">
          <div class="row mb-2">
            <div class="col-4 col-md-2">
              <label class="mb-0">統一編號</label>
            </div>
            <div class="col-8 col-md-10">
              <p class="mb-0">
                {{ deliveryItem.taxId }}
              </p>
            </div>
          </div>
        </div>
        <div class="col-12">
          <div class="row mb-2">
            <div class="col-4 col-md-2">
              <label class="mb-0">收件人</label>
            </div>
            <div class="col-8 col-md-10">
              <p class="mb-0">
                {{ deliveryItem.recipient }}
              </p>
            </div>
          </div>
        </div>
        <div class="col-12">
          <div class="row mb-2">
            <div class="col-4 col-md-2">
              <label class="mb-0">聯絡電話</label>
            </div>
            <div class="col-8 col-md-10">
              <p class="mb-0">
                {{ deliveryItem.phone }}
              </p>
            </div>
          </div>
        </div>

        <div class="col-12">
          <div class="row mb-2">
            <div class="col-4 col-md-2">
              <label class="mb-0 font-weight-bold mt-3">物流信息</label>
            </div>
            <div class="col-8 col-md-10">
              <a
                :style="{
                  display:
                    orderInfo.delivery.trackingUrl === '' ? 'none' : 'block'
                }"
                :href="orderInfo.delivery.trackingUrl"
                target="_blank"
                class="mb-0 text-danger font-weight-bold text-decoration-underline"
                >物流單號：{{ orderInfo.delivery.trackingNo }}</a
              >
            </div>
          </div>
        </div>
        <div class="col-12">
          <div class="row mb-2">
            <div class="col-4 col-md-2">
              <label class="mb-0">收貨地址：</label>
            </div>
            <div class="col-8 col-md-10">
              <p class="mb-0">{{ deliveryItem.address }}</p>
            </div>
          </div>
        </div>
        <div class="col-12">
          <div class="row mb-2">
            <div class="col-4 col-md-2">
              <label class="mb-0">寄送套數：</label>
            </div>
            <div class="col-8 col-md-10">
              <p class="mb-0">{{ deliveryItem.orderQty }}</p>
            </div>
          </div>
        </div>
        <div class="col-12">
          <div class="row mb-2">
            <div class="col-4 col-md-2">
              <label class="mb-0">送貨方式：</label>
            </div>
            <div class="col-8 col-md-10">
              <Formatter
                data-type="deliveryCompany"
                :origin-data="orderInfo.delivery.company"
                class="mb-0"
              >
                <template #default="propsData">
                  {{ propsData.formatData }}
                </template>
              </Formatter>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { initOrderInfo } from '@/assets/js/initData'
import Formatter from '~/components/Formatter.vue'
import { NumberComma } from '@/assets/js/tool'

export default {
  components: { Formatter },
  beforeRouteEnter(to, from, next) {
    next((vm) => {
      vm.pageRecord = from.query.page
    })
  },
  data() {
    return {
      orderInfo: {},
      fields: [
        { key: 'index', label: '編號', class: 'text-center' },
        { key: 'productName', label: '產品名稱', class: 'text-center' },
        { key: 'orderQty', label: '訂購數', class: 'text-center' },
        { key: 'unitPrice', label: '單價', class: 'text-center' },
        { key: 'price', label: '金額', class: 'text-center' }
      ]
    }
  },
  head() {
    return {
      title: '訂單細節 - 有良冊股份有限公司',
      meta: [
        {
          name: 'description',
          content: '訂單細節 - 查詢指定訂單的詳細資料'
        },
        {
          property: 'og:description',
          content: '訂單細節 - 查詢指定訂單的詳細資料'
        },
        {
          property: 'og:title',
          content: '訂單細節 - 有良冊股份有限公司'
        },
        {
          property: 'og:image',
          content: '/yooooobook.jpg'
        },
        {
          property: 'og:url',
          content: 'https://www.yooooobook.com/search/detail'
        }
      ]
    }
  },
  computed: {
    orderItems() {
      const orderList = this.orderInfo.orderList
      return orderList.map((order) => {
        return {
          productName: order.productName,
          orderQty: order.qty,
          unitPrice: order.unitPrice,
          price: order.totalPrice
        }
      })
    },
    totalPrice() {
      return NumberComma(this.orderInfo.totalPrice)
    }
  },
  created() {
    this.orderInfo = initOrderInfo()
  },
  mounted() {
    this.getOrderInfo()
  },
  methods: {
    async getOrderInfo() {
      const orderId = this.$route.params.orderId
      const uid = this.$cookies.get('userUid')
      const idToken = this.$cookies.get('id_token')

      const userOrderList = JSON.parse(
        JSON.stringify(this.$store.state.userOrderList)
      )

      const orderDetailFilter = userOrderList.filter((item) => {
        return item.orderId === orderId
      })

      if (!uid) return

      let orderDetail

      if (orderDetailFilter.length > 0) {
        // vuex的 state.orders 「有資料」時
        orderDetail = orderDetailFilter[0]
      } else {
        // vuex的 state.orders 「無資料」時
        try {
          const { data } = await this.$store.dispatch('getOrderInfo', {
            orderId,
            uid,
            idToken
          })
          orderDetail = data
        } catch (error) {
          // eslint-disable-next-line no-console
          console.log(error)
        }
      }
      orderDetail.isEdit = false
      this.orderInfo = orderDetail
    }
  }
}
</script>

<style lang="scss" scoped>
::v-deep(
    .table.b-table.b-table-stacked-md > tbody > tr > [data-label]::before
  ) {
  text-align: center;
}

.text-decoration-underline {
  text-decoration: underline;
}

.border-md {
  @include media-w768 {
    border: 1px solid #dee2e6;
  }
}
</style>
