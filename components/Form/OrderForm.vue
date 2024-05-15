<template>
  <div>
    <b-form novalidate @submit.prevent="onSubmit" @reset.prevent="onReset">
      <b-container fluid>
        <b-row cols="1" cols-md="2" cols-lg="3">
          <b-col v-for="(mainItemInputSetup, idx) in mainInputSetup" :key="idx">
            <b-form-group
              :label="mainItemInputSetup.title"
              :label-for="mainItemInputSetup.id"
            >
              <b-form-input
                :id="mainItemInputSetup.id"
                v-model="mainInputValue[mainItemInputSetup.attrName]"
                :type="mainItemInputSetup.type"
                :required="mainItemInputSetup.required"
                :placeholder="mainItemInputSetup.placeholder"
                trim
              ></b-form-input>

              <b-form-invalid-feedback
                :state="mainInputValueValidation[mainItemInputSetup.attrName]"
              >
                {{ mainItemInputSetup.invalidFeedback }}
              </b-form-invalid-feedback>
            </b-form-group>
          </b-col>
        </b-row>
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
    formData: {
      type: Object,
      default() {
        return {
          recipient: '',
          address: '',
          phone: '',
          email: '',
          account: '',
          buyer: '',
          taxId: '',
          buyerRemark: ''
        }
      }
    },
    inputSetup: {
      type: Array,
      required: true
    },
    btnSetup: {
      type: Array,
      required: true
    }
  },
  data() {
    return {
      mainInputValue: {},
      mainInputSetup: this.inputSetup
    }
  },
  computed: {
    mainInputValueValidation() {
      const validations = {}
      const _this = this
      const inputs = this.mainInputValue
      const propNames = Object.keys(inputs)
      propNames.forEach((propName) => {
        const inputValue = inputs[propName]
        validations[propName] = _this.inputValidate(propName, inputValue)
      })

      return validations
    }
  },
  created() {
    this.mainInputValue = JSON.parse(JSON.stringify(this.formData))
  },

  methods: {
    onSubmit() {
      const validations = Object.values(this.mainInputValueValidation)
      const isReady = !validations.includes(false)
      if (isReady) {
        this.$emit('submitEvent', this.mainInputValue)
      } else {
        this.$emit('toastEvent', '送出失敗', '請確認資料是否填寫完整')
      }
    },
    onReset() {
      this.$emit('resetEvent')
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
    }
  }
}
</script>
