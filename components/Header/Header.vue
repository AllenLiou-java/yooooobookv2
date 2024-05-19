<template>
  <div class="header">
    <div class="container">
      <h1 class="logo m-0"><nuxt-link to="/">Yooooobook</nuxt-link></h1>

      <div class="d-flex align-items-center position-relative">
        <div class="toggleBtn" @click="toggleNavbar"></div>

        <ul class="navigation">
          <li @click="moveToTop()">
            <nuxt-link to="/" exact>首頁</nuxt-link>
          </li>
          <li>
            <nuxt-link to="/products">預購書籍</nuxt-link>
          </li>
          <li>
            <nuxt-link to="/search?page=1">訂單查詢</nuxt-link>
          </li>
          <li>
            <nuxt-link to="/file" exact>檔案下載</nuxt-link>
          </li>

          <li v-if="isAdministrator">
            <nuxt-link to="/admin/search?page=1">訂單管理</nuxt-link>
          </li>

          <li v-if="eventType.event_en === 'login'">
            <button
              class="btnLogin-popup"
              @click="openModal(eventType.event_en)"
            >
              {{ eventType.event_ch }}
            </button>
          </li>
          <li v-if="eventType.event_en === 'logout'">
            <button
              class="btnLogout-popup"
              @click="openModal(eventType.event_en)"
            >
              {{ eventType.event_ch }}
            </button>
          </li>
        </ul>

        <CartPanel />
      </div>
    </div>
  </div>
</template>

<script>
import { lockScroll, unlockScroll } from '@/assets/js/tool'
import CartPanel from '~/components/Sidebar/CartPanel.vue'

export default {
  // eslint-disable-next-line vue/no-reserved-component-names
  name: 'Header',
  components: {
    CartPanel
  },
  computed: {
    eventType() {
      const isUserLoggedIn = this.$store.state.isUserLoggedIn
      if (isUserLoggedIn) {
        return {
          event_ch: '登出',
          event_en: 'logout'
        }
      } else {
        return {
          event_ch: '登入',
          event_en: 'login'
        }
      }
    },
    isAdministrator() {
      return this.$store.state.isAdministrator
    }
  },
  mounted() {
    window.addEventListener('scroll', () => {
      // toggleHeader
      const header = document.querySelector('.header')
      header.classList.toggle('sticky', window.scrollY > 0)
    })
  },
  methods: {
    moveToTop() {
      window.scrollTo({
        top: 0,
        behavior: 'smooth'
      })
    },
    toggleNavbar() {
      const toggleBtn = document.querySelector('.toggleBtn')
      const navigation = document.querySelector('.navigation')

      toggleBtn.classList.toggle('active')
      navigation.classList.toggle('active')

      const isToggleBtnActive = toggleBtn.classList.contains('active')
      if (isToggleBtnActive) {
        this.navigationItemListenerHandler('add')
        lockScroll()
      } else {
        this.navigationItemListenerHandler('remove')
        unlockScroll()
      }
    },
    navigationItemListenerHandler(action) {
      const activeItem = document.querySelectorAll('.navigation li')
      const toggleBtn = document.querySelector('.toggleBtn')
      const navigation = document.querySelector('.navigation')

      activeItem.forEach((item) => {
        if (action === 'add') {
          item.addEventListener('click', removeActiveMode, true)
        } else {
          item.removeEventListener('click', removeActiveMode, true)
        }
      })

      function removeActiveMode() {
        toggleBtn.classList.remove('active')
        navigation.classList.remove('active')
        unlockScroll()
      }
    },
    openModal(event) {
      if (event === 'login') {
        this.$emit('openLoginModal', true)
      } else {
        this.$emit('openLogoutModal', true)
      }

      lockScroll()
    }
  }
}
</script>

<style lang="scss" scoped>
// @import './header.scss';
@import '@/assets/scss/components/Header/header.scss';
</style>
