<template>
  <div>
    <b-form novalidate @submit.prevent="onSubmit" @reset.prevent="onReset">
      <b-container fluid>
        <b-row cols="1" cols-md="2" cols-lg="3">
          <b-col v-for="(item, idx) in formInputSetup" :key="idx">
            <b-form-group :label="item.title" :label-for="item.id">
              <b-form-input
                :id="item.id"
                v-model="inputValue[item.attrName]"
                :state="inputValueValidation[item.attrName]"
                :type="item.type"
                :required="item.required"
                :placeholder="item.placeholder"
                trim
              ></b-form-input>

              <b-form-invalid-feedback
                :state="inputValueValidation[item.attrName]"
              >
                {{ item.invalidFeedback }}
              </b-form-invalid-feedback>
            </b-form-group>
          </b-col>
          <b-col v-if="orderMode !== 'single'">
            <b-form-group label="數量" class="mb-0 p-0">
              <b-form-input
                id="orderQty"
                v-model.number="inputValue.mainOrderQty"
                type="number"
                :state="orderQtyValidation"
                required
                placeholder="請輸入此地址寄送套數"
                min="1"
              ></b-form-input>
            </b-form-group>
          </b-col>
          <b-col v-if="orderMode === 'single'">
            <b-form-group label="備註" class="mb-0 p-0">
              <b-form-textarea
                id="textarea"
                v-model="inputValue.buyerRemark"
                placeholder="若分別開立多張發票，請在此備註〔填寫格式：發票抬頭 – 統編 – 套數〕"
                rows="3"
                max-rows="8"
                trim
              ></b-form-textarea>
            </b-form-group>
          </b-col>
        </b-row>

        <template v-if="orderMode === 'single'">
          <div
            v-for="(
              invoiceInputSetupItem, setupItemIdx
            ) in invoiceInputSetupGroup"
            :key="setupItemIdx"
          >
            <p class="mb-2 font-weight-bold" style="font-size: 24px">
              發票 {{ setupItemIdx + 1 }}
            </p>
            <b-row cols="1" cols-md="2" cols-lg="3" class="position-relative">
              <Close
                class="text-dark rounded position-absolute"
                style="
                  width: 28px;
                  height: 28px;
                  font-size: 28px;
                  right: 10px;
                  top: 10px;
                  cursor: pointer;
                "
                @click="removeDeliveryItem(setupItemIdx)"
              />
              <b-col
                v-for="(invoiceInputSetup, idx) in invoiceInputSetupItem"
                :key="idx"
              >
                <b-form-group
                  :id="'fieldset-' + idx"
                  :label="invoiceInputSetup.title"
                  :label-for="invoiceInputSetup.id"
                >
                  <template v-if="invoiceInputSetup.attrName === 'invoiceItem'">
                    <b-form-input
                      :id="invoiceInputSetup.id"
                      v-model="
                        invoiceInputValue(setupItemIdx).productList[0].orderQty
                      "
                      :state="
                        invoiceItemValidation(setupItemIdx)[
                          invoiceInputSetup.id
                        ]
                      "
                      :type="invoiceInputSetup.type"
                      :required="invoiceInputSetup.required"
                      :placeholder="invoiceInputSetup.placeholder"
                      :min="invoiceInputSetup.type === 'number' ? 0 : null"
                      trim
                    ></b-form-input>
                  </template>
                  <template v-else>
                    <b-form-input
                      :id="invoiceInputSetup.attrName"
                      v-model="
                        invoiceInputValue(setupItemIdx)[
                          invoiceInputSetup.attrName
                        ]
                      "
                      :state="
                        invoiceItemValidation(setupItemIdx)[
                          invoiceInputSetup.attrName
                        ]
                      "
                      :type="invoiceInputSetup.type"
                      :required="invoiceInputSetup.required"
                      :placeholder="invoiceInputSetup.placeholder"
                      trim
                    ></b-form-input>
                  </template>

                  <!-- <b-form-invalid-feedback
                    :state="invoiceItemValidation[setupItemIdx][item.attrName]"
                  >
                    {{ item.invalidFeedback }}
                  </b-form-invalid-feedback> -->
                </b-form-group>
              </b-col>
              <!-- <b-col>
                <b-form-group label="數量" class="mb-0 p-0">
                  <b-form-input
                    id="deliveryListOrderQty"
                    v-model.number="
                      inputValue.invoiceList[setupItemIdx].qty
                    "
                    type="number"
                    :state="deliveryItemOrderQtyValidation(setupItemIdx)"
                    required
                    placeholder="請輸入此地址寄送套數"
                    min="1"
                  ></b-form-input>
                </b-form-group>
              </b-col> -->
            </b-row>
          </div>
        </template>
        <template v-else>
          <div
            v-for="(
              deliveryInputSetupItem, setupItemIdx
            ) in deliveryInputSetupGroup"
            :key="setupItemIdx"
          >
            <p class="mb-2 font-weight-bold" style="font-size: 24px">
              地址 {{ setupItemIdx + 2 }}
            </p>
            <b-row cols="1" cols-md="2" cols-lg="3" class="position-relative">
              <Close
                class="text-dark rounded position-absolute"
                style="
                  width: 28px;
                  height: 28px;
                  font-size: 28px;
                  right: 10px;
                  top: 10px;
                  cursor: pointer;
                "
                @click="removeDeliveryItem(setupItemIdx)"
              />
              <b-col
                v-for="(item, setupIdx) in deliveryInputSetupItem"
                :key="setupIdx"
              >
                <b-form-group
                  :id="'fieldset-' + setupIdx"
                  :label="item.title"
                  :label-for="item.id"
                >
                  <b-form-input
                    :id="item.id"
                    v-model="
                      inputValue.otherDeliveryList[setupItemIdx][item.attrName]
                    "
                    :state="
                      minorDeliveryItemValidation(setupItemIdx)[item.attrName]
                    "
                    :type="item.type"
                    :required="item.required"
                    :placeholder="item.placeholder"
                    trim
                  ></b-form-input>

                  <b-form-invalid-feedback
                    :state="
                      minorDeliveryItemValidation(setupItemIdx)[item.attrName]
                    "
                  >
                    {{ item.invalidFeedback }}
                  </b-form-invalid-feedback>
                </b-form-group>
              </b-col>
              <b-col>
                <b-form-group label="數量" class="mb-0 p-0">
                  <b-form-input
                    id="deliveryListOrderQty"
                    v-model.number="
                      inputValue.otherDeliveryList[setupItemIdx].orderQty
                    "
                    type="number"
                    :state="deliveryItemOrderQtyValidation(setupItemIdx)"
                    required
                    placeholder="請輸入此地址寄送套數"
                    min="1"
                  ></b-form-input>
                </b-form-group>
              </b-col>
            </b-row>
          </div>
        </template>

        <client-only>
          <template v-if="orderMode === 'single'">
            <b-col>
              <b-button variant="success" @click="addInvoiceItem"
                ><Plus class="mr-2" />新增發票資訊</b-button
              >
            </b-col>
          </template>
          <template v-else>
            <b-col>
              <b-button variant="success" @click="addDeliveryItem"
                ><Plus class="mr-2" />新增地址</b-button
              >
            </b-col>
          </template>
        </client-only>
      </b-container>

      <div class="d-flex justify-content-end">
        <b-button
          v-for="(btnItem, idx) in btnSetup"
          :key="idx"
          :type="btnItem.type"
          :variant="btnItem.variant"
          class="ml-3"
          >{{ btnItem.btnName }}</b-button
        >
      </div>
    </b-form>
  </div>
</template>

<script>
export default {
  props: {
    inputSetup: {
      type: Object,
      required: true,
    },
    btnSetup: {
      type: Array,
      required: true,
    },
    formData: {
      type: Object,
      default() {
        return {
          buyer: '',
          recipient: '',
          address: '',
          email: '',
          phone: '',
          taxId: '',
          account: '',
          buyerRemark: '',
          otherDeliveryList: [],
          invoiceList: [],
          mainOrderQty: null,
          totalOrderQty: null,
        }
      },
    },
    orderMode: {
      type: String,
      default() {
        return 'single'
      },
    },
    orderList: {
      type: Array,
      default() {
        return []
      },
    },
  },
  data() {
    return {
      // inputValue: this.formData,
      deliveryInputSetup: [
        {
          title: '收件人姓名',
          type: 'text',
          placeholder: '請輸入姓名',
          attrName: 'recipient',
          required: true,
          invalidFeedback: '請輸入收件人姓名',
        },
        {
          title: '收件人地址',
          type: 'text',
          placeholder: '請輸入地址',
          attrName: 'address',
          required: true,
          invalidFeedback: '請輸入地址',
        },
        {
          title: '聯絡電話',
          type: 'tel',
          placeholder: '請輸入電話號碼',
          attrName: 'phone',
          required: true,
          invalidFeedback: '請輸入電話號碼',
        },
        {
          title: '發票抬頭',
          type: 'text',
          placeholder: '請輸入發票抬頭',
          attrName: 'buyer',
          required: true,
          invalidFeedback: '請輸入發票抬頭',
        },
        {
          title: '統一編號',
          type: 'text',
          placeholder: '請輸入統一編號(選填)',
          attrName: 'taxId',
          required: false,
          invalidFeedback: '請輸入8碼統一編號',
        },
      ],
      deliveryInputSetupGroup: [],
      // invoiceInputSetup: [
      //   {
      //     title: '發票抬頭',
      //     type: 'text',
      //     placeholder: '請輸入發票抬頭',
      //     attrName: 'buyer',
      //     required: true,
      //     invalidFeedback: '請輸入發票抬頭',
      //   },
      //   {
      //     title: '統一編號',
      //     type: 'text',
      //     placeholder: '請輸入統一編號(選填)',
      //     attrName: 'taxId',
      //     required: false,
      //     invalidFeedback: '請輸入8碼統一編號',
      //   },
      // ],
      invoiceInputSetupGroup: [],
    }
  },
  computed: {
    inputValue() {
      return this.formData
    },
    invoiceInputValue() {
      return (idx) => {
        return this.inputValue.invoiceList[idx]
      }
    },
    inputValueValidation() {
      const validations = {}
      const { invoiceList, otherDeliveryList, ...inputs } = this.inputValue
      const inputPropNames = Object.keys(inputs)

      inputPropNames.forEach((propName) => {
        const data = inputs[propName]
        validations[propName] = this.inputValidate(propName, data)
      })
      return validations
    },
    orderQtyValidation() {
      const data = this.inputValue.mainOrderQty
      return this.inputValidate('orderQty', data)
    },
    minorDeliveryItemValidation() {
      return (idx) => {
        const validations = {}
        const inputs = this.inputValue.otherDeliveryList[idx]
        const inputPropNames = Object.keys(inputs)
        inputPropNames.forEach((propName) => {
          const inputValue = inputs[propName]
          validations[propName] = this.inputValidate(propName, inputValue)
        })

        return validations
      }
    },
    deliveryItemOrderQtyValidation() {
      return (idx) => {
        const data = this.inputValue.otherDeliveryList[idx].orderQty
        return this.inputValidate('orderQty', data)
      }
    },
    invoiceItemValidation() {
      return (idx) => {
        const validations = {}
        const invoiceItem = this.inputValue.invoiceList[idx]
        const invoiceItemPropNames = Object.keys(invoiceItem)
        invoiceItemPropNames.forEach((propName) => {
          if (propName === 'productList') {
            const productList = invoiceItem[propName]
            productList.forEach((productItem) => {
              validations[productItem.productId] = this.inputValidate(
                'orderQty',
                productItem.orderQty
              )
            })
          } else {
            const inputValue = invoiceItem[propName]
            validations[propName] = this.inputValidate(propName, inputValue)
          }
        })

        return validations
      }
    },
    formInputSetup() {
      if (this.orderMode === 'single') {
        return this.inputSetup.singleAddressMode
      } else {
        return this.inputSetup.multiAddressMode
      }
    },
  },
  methods: {
    onSubmit() {
      const inputValidation = this.listValidator(this.inputValue)
      // 驗證deliveryList的內容
      const deliveryListInputValidation = this.deliveryListValidator()

      if (!inputValidation) return
      if (!deliveryListInputValidation) return
      if (!this.checkOrderQty()) return

      this.$emit('submitEvent', this.inputValue)
    },
    deliveryListValidator() {
      const deliveryList = this.inputValue.otherDeliveryList
      const result = []
      for (let i = 0; i < deliveryList.length; i++) {
        const item = deliveryList[i]
        const validatorItem = this.listValidator(item)
        result.push(validatorItem)
      }
      return !result.includes(false)
    },
    listValidator(list) {
      const keyList = Object.keys(list)
      const resultMap = keyList.map((key) => {
        const input = list[key]
        return this.inputValidate(key, input)
      })
      return !resultMap.includes(false)
    },
    onReset() {
      this.$emit('resetEvent')
    },
    addDeliveryItem() {
      const deliveryInputSetupGroup = this.deliveryInputSetupGroup
      const deliveryInputSetup = this.deliveryInputSetup
      deliveryInputSetupGroup.push(deliveryInputSetup)

      const deliveryItem = {
        buyer: '',
        recipient: '',
        address: '',
        phone: '',
        taxId: '',
        orderQty: 1,
      }
      this.inputValue.otherDeliveryList.push(deliveryItem)
    },
    addInvoiceItem() {
      const invoiceItem = {
        buyer: '',
        taxId: '',
        productList: [],
      }

      const invoiceInputSetup = [
        {
          title: '發票抬頭',
          type: 'text',
          placeholder: '請輸入發票抬頭',
          attrName: 'buyer',
          required: true,
          invalidFeedback: '請輸入發票抬頭',
        },
        {
          title: '統一編號',
          type: 'text',
          placeholder: '請輸入統一編號(選填)',
          attrName: 'taxId',
          required: false,
          invalidFeedback: '請輸入8碼統一編號',
        },
      ]

      // const _this = this

      this.orderList.forEach((orderItem) => {
        invoiceItem.productList.push({
          name: orderItem.name,
          productId: orderItem.productId,
          orderQty: 0,
          price: orderItem.price.discount,
        })

        invoiceInputSetup.push({
          title: orderItem.name,
          type: 'number',
          placeholder: '請輸入此產品寄送套數',
          attrName: 'invoiceItem',
          id: orderItem.productId,
          required: true,
          invalidFeedback: '請輸入此產品寄送套數',
        })
      })

      this.inputValue.invoiceList.push(invoiceItem)
      this.invoiceInputSetupGroup.push(invoiceInputSetup)
    },
    removeDeliveryItem(index) {
      this.deliveryInputSetupGroup.splice(index, 1)
      this.inputValue.otherDeliveryList.splice(index, 1)
    },
    inputValidate(type, input) {
      const data = input
      if (type === 'buyer') {
        return data.length !== 0
      } else if (type === 'recipient') {
        return data.length !== 0
      } else if (type === 'address') {
        return data.length !== 0
      } else if (type === 'email') {
        const emailRule =
          // eslint-disable-next-line no-useless-escape
          /^([a-zA-Z0-9_\.\-\+])+\@(([a-zA-Z0-9\-])+\.)+([a-zA-Z0-9]{2,4})+$/
        return data.search(emailRule) !== -1
      } else if (type === 'phone') {
        const phoneRule =
          /^(\d{2,3}-?|\(\d{2,3}\))\d{3,4}-?\d{4}|09\d{2}(\d{6}|-\d{3}-\d{3})$/

        return data.search(phoneRule) !== -1
      } else if (type === 'taxId') {
        return data.length === 0 || data.length === 8
      } else if (type === 'account') {
        return data.length === 5
      } else if (type === 'orderQty') {
        return data > 0
      } else {
        return true
      }
    },
    clearDeliveryItem() {
      this.deliveryInputSetupGroup = []
      this.inputValue.otherDeliveryList = []
    },
    checkOrderQty() {
      const mainOrderQty = this.inputValue.mainOrderQty
      const minorOrderQty = this.inputValue.otherDeliveryList.map((item) => {
        return item.orderQty
      })

      const accumulateOrderQty = [mainOrderQty, ...minorOrderQty].reduce(
        (acc, cur) => {
          return acc + cur
        }
      )

      const totalOrderQty = this.inputValue.totalOrderQty

      if (accumulateOrderQty !== totalOrderQty) {
        this.$emit(
          'orderQtyError',
          '數量不符',
          '「地址寄送數量總和」與「總訂購數量」不符'
        )
        return false
      }

      return true
    },
  },
}
</script>
