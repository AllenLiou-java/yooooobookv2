<template>
  <div class="container py-4">
    <h1 v-if="error.statusCode === 404">頁面不存在</h1>
    <h1 v-else>頁面發生錯誤</h1>
    <div>
      <p>錯誤代碼： {{ error.statusCode }}</p>
      <p>錯誤訊息： {{ errorMessage }}</p>
    </div>
    <NuxtLink to="/" class="btn btn-secondary">Home page</NuxtLink>
  </div>
</template>

<script>
import APIHandler from '~/utils/apiHandler'
export default {
  props: {
    error: {
      type: Object,
      required: true
    }
  },
  computed: {
    errorMessage() {
      const { statusCode } = this.error

      return APIHandler.matchErrorMsg(statusCode, this.error)
    }
  }
  //   layout: "error", // you can set a custom layout for the error page
}
</script>
