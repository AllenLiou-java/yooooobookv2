<template>
  <div class="container py-4">
    <div class="cards d-flex flex-column flex-sm-row flex-wrap">
      <nuxt-link
        v-for="product in products"
        :key="product.productId"
        :to="'/products/' + product.productId"
        class="card d-flex flex-column justify-content-center align-items-center rounded px-2 py-3 border m-3"
      >
        <img class="mb-2" :src="product.imgSrc" alt="product-img" />
        <span class="mb-3">{{ product.name }}</span>
        <div class="btn-secondary align-self-end px-2 py-1 rounded">
          詳細資料
        </div>
      </nuxt-link>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      products: []
    }
  },
  head() {
    return {
      title: '預購書籍 - 有良冊股份有限公司',
      meta: [
        {
          // hid: 'description',
          name: 'description',
          content: '預購書籍 - 產品訂購頁面、方案促銷內容'
        },
        {
          // hid: 'og:description',
          property: 'og:description',
          content: '預購書籍 - 產品訂購頁面、方案促銷內容'
        },
        {
          // hid: 'og:title',
          property: 'og:title',
          content: '預購書籍 - 有良冊股份有限公司'
        },
        {
          // hid: 'og:image',
          property: 'og:image',
          content: '/yooooobook.jpg'
        },
        {
          // hid: 'og:url',
          property: 'og:url',
          content: 'https://www.yooooobook.com/order'
        }
      ]
    }
  },
  computed: {},
  mounted() {
    this.$store.dispatch('getProductList').then((productList) => {
      this.products = productList
    })
  }
}
</script>

<style lang="scss" scoped>
.cards {
  margin-left: -16px;
  margin-right: -16px;
}
.card {
  max-width: calc((100% - 128px) / 4);
  transition: ease 0.4s;

  img {
    width: 240px;
    height: 240px;
    object-fit: contain;
  }

  @include media-w900 {
    max-width: calc((100% - 96px) / 3);
  }

  @include media-w768 {
    max-width: calc((100% - 64px) / 2);
  }

  @include media-w414 {
    max-width: 100%;
  }

  &:hover {
    transform: translateY(-10px);
    box-shadow: rgba(50, 50, 93, 0.25) 0px 6px 12px -2px,
      rgba(0, 0, 0, 0.3) 0px 3px 7px -3px;
    cursor: pointer;
  }
}

.card:hover .btn-secondary {
  background-color: #001f30;
  border-color: #001623;
}
</style>
