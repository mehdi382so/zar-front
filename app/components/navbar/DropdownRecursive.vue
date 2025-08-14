<template>
  <!-- ========== Wrapper for a menu item ========== -->
  <li class="relative">
    <div
      ref="triggerEl"
      class="flex items-center gap-1 transition select-none"
      :class="inPanel ? 'px-4 py-2 rounded cursor-pointer hover:bg-gray-100' : 'py-1'"
      @mouseenter="onTriggerEnter"
      @mouseleave="onTriggerLeave"
    >
      
      <!-- ========== Link text ========== -->
      <NuxtLink :to="item.link" class="flex-1" @click.prevent>
        {{ item.text }}
      </NuxtLink>

      <!-- ========== Submenu indicator (arrow) ========== -->
      <svg
        v-if="item.submenu"
        xmlns="http://www.w3.org/2000/svg"
        class="h-4 w-4 mt-0.5 transition-transform"
        :class="{ 'rotate-90': open }"
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
    </div>
  </li>

  <!-- ========== Submenu panel ========== -->
  <Teleport v-if="item.submenu" to="body">
    <ul
      v-show="open"
      ref="submenuEl"
      class="fixed z-[70] rounded-xl shadow-xl p-3 min-w-[12rem] max-w-xs max-h-[80vh] overflow-auto bg-white text-gray-700 transition-opacity duration-150"
      :style="submenuStyle"
      @mouseenter="onPanelEnter"
      @mouseleave="onPanelLeave"
    >

      <!-- ========== Recursive call for submenus ========== -->
      <DropdownRecursive
        v-for="sub in item.submenu"
        :key="sub.text"
        :item="sub"
        :in-panel="true"
      />
    </ul>
  </Teleport>
</template>

<script setup>
import { ref, reactive, onMounted, onBeforeUnmount, provide, inject, nextTick } from "vue";

const props = defineProps({
  item: { type: Object, required: true },
  inPanel: { type: Boolean, default: false },
});

/* ========== Reactive states ========== */
const open = ref(false);
const triggerEl = ref(null);
const submenuEl = ref(null);
const submenuStyle = reactive({ top: "0px", left: "0px" });
let closeTimer = null;

/* ========== Inherit close/open chain from parent ========== */
const parentKeepOpenChain = inject("keepOpenChain", null);
const parentScheduleCloseChain = inject("scheduleCloseChain", null);

/* ========== Helpers ========== */
function cancelClose() { if (closeTimer) { clearTimeout(closeTimer); closeTimer = null }}
function scheduleClose() { cancelClose(); closeTimer = setTimeout(() => { open.value = false }, 150)}
function keepOpenChain() { cancelClose(); parentKeepOpenChain && parentKeepOpenChain()}
function scheduleCloseChain() { scheduleClose(); parentScheduleCloseChain && parentScheduleCloseChain()}

/* ========== Provide to children ========== */
provide("keepOpenChain", keepOpenChain);
provide("scheduleCloseChain", scheduleCloseChain);

/* ========== Position submenu relative to trigger ========== */
async function positionPanel() {
  if (!triggerEl.value || !submenuEl.value) return;
  const GAP = 8;
  const rect = triggerEl.value.getBoundingClientRect();
  const vw = window.innerWidth;
  const vh = window.innerHeight;

  if (props.inPanel) {
    submenuStyle.top = `${rect.top}px`;
    submenuStyle.left = `${rect.left - 200}px`;
  } else {
    submenuStyle.top = `${rect.bottom}px`;
    submenuStyle.left = `${rect.left}px`;
  }

  await nextTick();
  const panelRect = submenuEl.value.getBoundingClientRect();
  const pw = panelRect.width;
  const ph = panelRect.height;

  let top, left;
  if (props.inPanel) {
    left = rect.left - GAP - pw;
    if (left < 8) left = rect.right + GAP;
    top = rect.top;
  } else {
    top = rect.bottom + GAP;
    left = rect.left;
    if (left + pw > vw - 8) left = Math.max(8, vw - 8 - pw);
  }

  if (top + ph > vh - 8) {
    top = Math.max(8, vh - 8 - ph);
  }

  if (top < 8) top = 8;
  submenuStyle.top = `${top}px`;
  submenuStyle.left = `${left}px`;
}

/* ========== Event handler ========== */
function onTriggerLeave() { scheduleClose() }
function onPanelEnter() { keepOpenChain() }
function onPanelLeave() { scheduleCloseChain() }
function onScrollOrResize() { if (open.value) positionPanel() }
function onTriggerEnter() {
  if (!props.item.submenu) return;
  open.value = true;
  keepOpenChain();
  nextTick(positionPanel);
}

onMounted(() => {
  window.addEventListener("scroll", onScrollOrResize, true);
  window.addEventListener("resize", onScrollOrResize, true);
});

onBeforeUnmount(() => {
  cancelClose();
  window.removeEventListener("scroll", onScrollOrResize, true);
  window.removeEventListener("resize", onScrollOrResize, true);
});
</script>
