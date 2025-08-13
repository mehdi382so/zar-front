<template>
  <header class="fixed top-0 left-0 right-0 z-50 shadow-lg transition-all duration-300 bg-white">
    
    <section class="flex justify-between items-center px-6 py-2 transition-all duration-300">
      
      <button @click="isMenuOpen = !isMenuOpen" class="cursor-pointer xl:hidden focus:outline-none">
        <svg xmlns="http://www.w3.org/2000/svg" class="h-7 w-7 text-gray-700" fill="none" viewBox="0 0 24 24" stroke="currentColor">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 6h16M4 12h16M4 18h16"/>
        </svg>
      </button>

      <NuxtLink to="/" class="hidden xl:inline-block bg-gradient-to-r from-blue-500 to-indigo-600 text-white px-10 pb-3 pt-2 rounded-full hover:opacity-90 transition font-medium shadow-md">
        ورود
      </NuxtLink>

      <div class="hidden lg:flex flex-1 max-w-md mx-4 relative">
        <input type="text" placeholder="عبارت جست و جو را وارد نمایید .." class="w-full border border-gray-300 rounded-full pl-10 pr-4 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-blue-500 focus:bg-blue-50 focus:border-blue-500 transition"/>
        <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 absolute left-3 top-2.5 text-gray-400" fill="none" viewBox="0 0 24 24" stroke="currentColor">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M21 21l-4.35-4.35m0 0A7.5 7.5 0 1110.5 3a7.5 7.5 0 016.15 13.65z"/>
        </svg>
      </div>

      <div class="flex items-center gap-4">
        <img src="/zar-logo.png" alt="logo of zar spring factory" class="h-10 w-auto object-contain"/>
        <img src="/saipa-logo.png" alt="logo of saipa group factory" class="h-10 w-auto object-contain"/>
      </div>
    </section>

    <nav class="bg-gradient-to-b from-blue-600 to-indigo-700 transition-all duration-300">
      
      <ul :class="['hidden xl:flex justify-center items-center gap-8 text-white px-6 py-4 font-medium tracking-wide duration-200', isScrolled ? 'h-12 text-xs' : 'h-fit text-base']">
        <DropdownRecursive v-for="item in menuItems" :key="item.text" :item="item"/>
      </ul>

      <transition name="slide">
        <div v-if="isMenuOpen" class="fixed top-0 right-0 h-screen w-72 bg-white shadow-lg z-50 flex flex-col p-4 overflow-y-auto scrollbar-hide">
          
          <button @click="closeMenu" class="cursor-pointer self-end mb-4 text-gray-500 hover:text-gray-800">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"/>
            </svg>
          </button>

          <div class="flex flex-col gap-3 mb-5">
            <input type="text" placeholder="عبارت جست و جو را وارد نمایید .." class="block lg:hidden border border-gray-300 rounded-full px-4 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-blue-500 focus:bg-blue-50"/>
            <NuxtLink to="/login" class="bg-gradient-to-r from-blue-500 to-indigo-600 text-white px-10 pb-3 pt-2 rounded-full text-center hover:opacity-90 transition font-medium shadow-md">
              ورود
            </NuxtLink>
          </div>

          <ul class="flex flex-col gap-2">
            <MobileDropdownRecursive v-for="item in menuItems" :key="item.text" :item="item"/>
          </ul>
        </div>
      </transition>
    </nav>
  </header>
</template>

<script setup>

import { ref, onMounted, onUnmounted } from 'vue'
import DropdownRecursive from './DropdownRecursive.vue'
import MobileDropdownRecursive from './MobileDropdownRecursive.vue'

const isMenuOpen = ref(false)
const isScrolled = ref(false)

const menuItems = [
  {text: 'حاکمیت شرکتی', link: '#'},
  {text: 'درباره زر', link: '#', submenu: [
    {text: 'درباره زر', link: '#', submenu: [
      {text: 'درباره زر تهران', link: '#'},
      {text: 'درباره زر گلپایگان', link: '#'},
      {text: 'درباره زر سیستان', link: '#'},
    ]},
    {text: 'ماموریت', link: '#'},
    {text: 'چشم انداز', link: '#'},
    {text: 'ارزش های سازمانی', link: '#'},
    {text: 'خط مشی', link: '#'},
    {text: 'مدیران ارشد', link: '#'},
    {text: 'معاونین و مدیران', link: '#'},
    {text: 'اخبار', link: '#'}
  ]},
  {text: 'محصولات', link: '#', submenu: [
    {text: 'محصولات منتخب', link: '#'},
    {text: 'محصولات شرکت زر تهران', link: '#'},
    {text: 'محصولات شرکت زر گلپایگان', link: '#'},
    {text: 'محصولات شرکت زر سیستان', link: '#'}
  ]},
  {text: 'تکنولوژی', link: '#', submenu: [
      {text: 'R & D', link: '#'},
      {text: 'ماشین آلات', link: '#'}
  ]},
  {text: 'کیفیت', link: '#', submenu: [
      {text: 'آزمایشگاه', link: '#'},
      {text: 'سیستم های کیفیت', link: '#'},
      {text: 'کیفیت', link: '#'},
      {text: 'گواهی نامه ها', link: '#'}
  ]},
  {text: 'آموزش', link: '#', submenu: [
      {text: 'مقالات', link: '#'},
      {text: 'درخواست کارورزی', link: '#'}
  ]},
  {text: 'درخواست نمایندگی', link: '#'},
  {text: 'تماس با ما', link: '#'},
  {text: 'صدای مشتری', link: '#'},
  {text: 'مسئولیت ها', link: '#', submenu: [
    {text: 'مسئولیت های اجتماعی و زیست محیطی', link: '#'}
  ]}
]

const closeMenu = () => {
  isMenuOpen.value = false
}

const handleScroll = () => {
  isScrolled.value = window.scrollY > 80
}

onMounted(() => {
  window.addEventListener('scroll', handleScroll)
})

onUnmounted(() => {
  window.removeEventListener('scroll', handleScroll)
})

</script>

<style scoped>

.slide-enter-active,
.slide-leave-active {
  transition: all 0.3s ease;
}

.slide-enter-from,
.slide-leave-to {
  opacity: 0;
  transform: translateY(-10px);
}

</style>