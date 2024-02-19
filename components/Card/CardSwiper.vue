<template>
  <div>
    <div class="swiper">
      <!-- Additional required wrapper -->
      <div class="swiper-wrapper">
        <!-- Slides -->
        <div
          v-for="(feature, index) in features"
          :key="index"
          class="swiper-slide"
        >
          <Card :title="feature.title" :description="feature.description" />
        </div>
      </div>
    </div>

    <!-- If we need navigation buttons -->
    <!-- <div class="swiper-button-prev"></div>
		<div class="swiper-button-next"></div> -->

    <!-- If we need pagination -->
    <div class="swiper-pagination"></div>
  </div>
</template>

<script>
// import Swiper from 'swiper/js/swiper.min.js'
import Swiper from 'swiper'
import 'swiper/css/swiper.min.css'
import Card from '@/components/Card/Card.vue'

export default {
  name: 'CardSwiper',
  components: {
    Card,
  },
  props: {
    features: {
      type: Array,
      default() {
        const data = [
          {
            title: '',
            description: '',
          },
        ]
        return data
      },
    },
  },
  mounted() {
    // eslint-disable-next-line no-unused-vars
    const mySwiper = new Swiper('.swiper', {
      initialSlide: 0,
      slidesPerView: 'auto',
      spaceBetween: 24,
      roundLengths: true,
      autoplay: {
        delay: 2000,
        stopOnLastSlide: false,
        disableOnInteraction: true,
      },
      // effect: "coverflow",
      // centeredSlides: true,
      observer: true,
      observeParents: true,
      grabCursor: true,
      pagination: {
        el: '.swiper-pagination',
        type: 'bullets',
        bulletElement: 'li',
      },
      // navigation: {
      //   nextEl: '.swiper-button-next',
      //   prevEl: '.swiper-button-prev',
      // },
    })
  },
}
</script>

<style lang="scss" scoped>
@import '@/assets/scss/components/Card/cardSwiper.scss';
</style>
