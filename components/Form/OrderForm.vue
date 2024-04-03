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
        </b-row>
      </b-container>
    </b-form>
  </div>
</template>

<script>
export default {
  props: {
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
  },
  data() {
    return {
      orderMode: 'single',
      // inputSetup: {
      //   singleAddressMode: [
      //     {
      //       title: '收件人姓名',
      //       type: 'text',
      //       placeholder: '請輸入姓名',
      //       attrName: 'recipient',
      //       required: true,
      //       invalidFeedback: '請輸入收件人姓名',
      //       id: 'recipient',
      //     },
      //     {
      //       title: '收件人地址',
      //       type: 'text',
      //       placeholder: '請輸入地址',
      //       attrName: 'address',
      //       required: true,
      //       invalidFeedback: '請輸入地址',
      //       id: 'address',
      //     },
      //     {
      //       title: 'Email',
      //       type: 'email',
      //       placeholder: 'example@gmail.com',
      //       attrName: 'email',
      //       required: true,
      //       invalidFeedback: '請確認email格式',
      //       id: 'email',
      //     },
      //     {
      //       title: '聯絡電話',
      //       type: 'tel',
      //       placeholder: '請輸入電話號碼',
      //       attrName: 'phone',
      //       required: true,
      //       invalidFeedback: '請輸入電話號碼',
      //       id: 'tel',
      //     },
      //     {
      //       title: '帳戶後5碼',
      //       type: 'text',
      //       placeholder: '請輸入金融帳戶後5碼',
      //       attrName: 'account',
      //       required: true,
      //       invalidFeedback: '請確認輸入之數字',
      //       id: 'account',
      //     },
      //   ],
      //   multiAddressMode: [
      //     {
      //       title: '收件人姓名',
      //       type: 'text',
      //       placeholder: '請輸入姓名',
      //       attrName: 'recipient',
      //       required: true,
      //       invalidFeedback: '請輸入收件人姓名',
      //       id: 'recipient',
      //     },
      //     {
      //       title: '收件人地址',
      //       type: 'text',
      //       placeholder: '請輸入地址',
      //       attrName: 'address',
      //       required: true,
      //       invalidFeedback: '請輸入地址',
      //       id: 'address',
      //     },
      //     {
      //       title: 'Email',
      //       type: 'email',
      //       placeholder: 'example@gmail.com',
      //       attrName: 'email',
      //       required: true,
      //       invalidFeedback: '請確認email格式',
      //       id: 'email',
      //     },
      //     {
      //       title: '聯絡電話',
      //       type: 'tel',
      //       placeholder: '請輸入電話號碼',
      //       attrName: 'phone',
      //       required: true,
      //       invalidFeedback: '請輸入電話號碼',
      //       id: 'tel',
      //     },
      //     {
      //       title: '帳戶後5碼',
      //       type: 'text',
      //       placeholder: '請輸入金融帳戶後5碼',
      //       attrName: 'account',
      //       required: true,
      //       invalidFeedback: '請確認輸入之數字',
      //       id: 'account',
      //     },
      //     {
      //       title: '發票抬頭',
      //       type: 'text',
      //       placeholder: '請輸入發票抬頭',
      //       attrName: 'buyer',
      //       required: true,
      //       invalidFeedback: '請輸入發票抬頭',
      //       id: 'buyer',
      //     },
      //     {
      //       title: '統一編號',
      //       type: 'text',
      //       placeholder: '請輸入統一編號(選填)',
      //       attrName: 'taxId',
      //       required: false,
      //       invalidFeedback: '請輸入8碼統一編號',
      //       id: 'taxId',
      //     },
      //   ],
      // },
    }
  },
  computed: {
    formInputSetup() {
      const mode = this.orderMode
      const recipient = {
        title: '收件人姓名',
        type: 'text',
        placeholder: '請輸入姓名',
        attrName: 'recipient',
        required: true,
        invalidFeedback: '請輸入收件人姓名',
        id: 'recipient',
      }

      const address = {
        title: '收件人地址',
        type: 'text',
        placeholder: '請輸入地址',
        attrName: 'address',
        required: true,
        invalidFeedback: '請輸入地址',
        id: 'address',
      }

      const email = {
        title: 'Email',
        type: 'email',
        placeholder: 'example@gmail.com',
        attrName: 'email',
        required: true,
        invalidFeedback: '請確認email格式',
        id: 'email',
      }

      const tel = {
        title: '聯絡電話',
        type: 'tel',
        placeholder: '請輸入電話號碼',
        attrName: 'phone',
        required: true,
        invalidFeedback: '請輸入電話號碼',
        id: 'tel',
      }
      const account = {
        title: '帳戶後5碼',
        type: 'text',
        placeholder: '請輸入金融帳戶後5碼',
        attrName: 'account',
        required: true,
        invalidFeedback: '請確認輸入之數字',
        id: 'account',
      }
      const buyer = {
        title: '發票抬頭',
        type: 'text',
        placeholder: '請輸入發票抬頭',
        attrName: 'buyer',
        required: true,
        invalidFeedback: '請輸入發票抬頭',
        id: 'buyer',
      }
      const taxId = {
        title: '統一編號',
        type: 'text',
        placeholder: '請輸入統一編號(選填)',
        attrName: 'taxId',
        required: false,
        invalidFeedback: '請輸入8碼統一編號',
        id: 'taxId',
      }

      if (mode === 'single') {
        return [recipient, address, email, tel, account]
      } else {
        return [recipient, address, email, tel, account, buyer, taxId]
      }
    },
  },
  methods: {},
}
</script>
