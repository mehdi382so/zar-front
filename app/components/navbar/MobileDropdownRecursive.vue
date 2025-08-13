<template>
  <li>
    <div @click="toggle" class="flex justify-between items-center px-4 py-2 cursor-pointer font-medium text-gray-600 hover:bg-gray-100 rounded transition select-none">
      <NuxtLink :to="item.link" class="flex-1" @click.stop>
        {{ item.text }}
      </NuxtLink>

      <button v-if="item.submenu" class="cursor-pointer ml-2 focus:outline-none opacity-50 hover:opacity-100 transition-opacity" @click.stop="toggle">
        <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 transition duration-200" :class="{ 'rotate-180': isOpen }" fill="none" viewBox="0 0 24 24" stroke="currentColor">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 9l-7 7-7-7" />
        </svg>
      </button>
    </div>

    <ul v-if="item.submenu && isOpen" class="mr-4 pl-4 border-r-2 border-gray-300">
      <MobileDropdownRecursive v-for="sub in item.submenu" :key="sub.text" :item="sub"/>
    </ul>
  </li>
</template>

<script setup>
import { ref } from 'vue'

defineProps({
  item: Object
})

const isOpen = ref(false)

function toggle() {
  isOpen.value = !isOpen.value
}

</script>
