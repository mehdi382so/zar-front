<template>
  <!-- ========== Wrapper for a mobile menu item ========== -->
  <li>
    <!-- ========== Row for click/hover on the item ========== -->
    <div
      ref="menuItem"
      @click="toggle"
      class="item flex justify-between items-center px-4 py-2 cursor-pointer font-medium text-gray-600 hover:bg-gray-100 rounded select-none"
    >
      <!-- ========== Link text ========== -->
      <NuxtLink :to="item.link" class="flex-1" @click.stop>
        {{ item.text }}
      </NuxtLink>

      <!-- ========== Arrow button for submenu ========== -->
      <button
        v-if="item.submenu"
        class="cursor-pointer ml-2 focus:outline-none opacity-50 hover:opacity-100 transition-opacity"
        @click.stop="toggle"
      >
        <svg
          xmlns="http://www.w3.org/2000/svg"
          class="h-5 w-5 transition duration-200"
          :class="{ 'rotate-180': isOpen }"
          fill="none"
          viewBox="0 0 24 24"
          stroke="currentColor"
        >
          <path
            stroke-linecap="round"
            stroke-linejoin="round"
            stroke-width="2"
            d="M19 9l-7 7-7-7"
          />
        </svg>
      </button>
    </div>

    <!-- ========== Submenu items ========== -->
    <ul
      v-if="showMenu"
      ref="submenu"
      class="mr-4 pl-4 border-r-2 border-gray-300 overflow-hidden"
    >
      <MobileDropdownRecursive
        v-for="(sub, i) in item.submenu"
        :key="sub.text"
        :item="sub"
      />
    </ul>
  </li>
</template>

<script setup>
import { ref, nextTick } from "vue";
import { gsap } from "gsap";

defineProps({
  item: Object,
});

/* ========== State variables ========== */
const isOpen = ref(false);
const showMenu = ref(false);
const submenu = ref(null);

/* ========== Toggle submenu ========== */
function toggle() {
  if (isOpen.value) {
    closeAnimation();
  } else {
    showMenu.value = true;
    nextTick(() => openAnimation());
  }
}

/* ========== Open animation: items slide in from right ========== */
function openAnimation() {
  const items = submenu.value.querySelectorAll(".item");
  gsap.fromTo(
    items,
    { x: 100, opacity: 0 },
    { x: 0, opacity: 1, duration: 0.4, stagger: 0.05, ease: "power2.out" }
  );
  isOpen.value = true;
}

/* ========== Close animation: bottom items close first ========== */
function closeAnimation() {
  if (!submenu.value) return;
  const items = Array.from(submenu.value.querySelectorAll(".item")).reverse();
  gsap.to(items, {
    x: 100,
    opacity: 0,
    duration: 0.3,
    stagger: 0.05,
    ease: "power2.in",
    onComplete: () => {
      showMenu.value = false;
      isOpen.value = false;
      gsap.set(submenu.value.querySelectorAll(".item"), { clearProps: "all" });
    }
  });
}
</script>
