<template>
  <div v-if="loading" class="loading-page">
    <p>Loading...</p>
  </div>
</template>

<script>
export default {
  data: () => ({
    loading: false
  }),
  methods: {
    start() {
      this.loading = true
    },
    finish() {
      this.loading = false
    }
  }
}
</script>

<style lang="scss" scoped>
.loading-page {
  position: fixed;
  display: flex;
  justify-content: center;
  align-items: center;
  // top: 0;
  // left: 0;
  width: 100%;
  height: 100%;
  background: rgba(255, 255, 255, 0.8);
  text-align: center;
  // padding-top: 200px;
  padding-bottom: 100px;
  font-size: 30px;
  font-family: sans-serif;
  z-index: 999;
}
</style>
