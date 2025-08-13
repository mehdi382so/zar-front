<script setup lang="ts">
import Footer from '~/components/Footer.vue';
import Navbar from '~/components/navbar/Navbar.vue';

</script>

<template>
  <Navbar />
  <slot />
  <Footer />
</template>