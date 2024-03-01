<script setup>
import { ref } from 'vue';
import Map from './components/Map.vue';
import Search from './components/Search.vue';
</script>

<template>
  <!-- <header>
    <img alt="Vue logo" class="logo" src="./assets/logo.svg" width="25" height="25" />
    <nav class="nav-bar">
      <a href="">Map</a>
      <a href="">About</a>
      <a href=""></a>
      <a href=""></a>
    </nav>
  </header> -->

  <main>
    <section class="wrapper">
      <Search />
      <Map msg="You did it!" />
    </section>
  </main>
</template>

<style scoped></style>
