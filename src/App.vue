<script setup>
import HomeView from "./views/HomeView.vue"
import Header from "../src/components/Header.vue"

</script>

<template>
  <div class="topHeader">
    <Header />
  </div>
  <div class="all">
    <RouterView />
  </div>
  <!-- <HomeView /> -->
</template>

<style scoped>
.headershow {
  width: 100vw;
  height: 15vh;
}
.all{
  text-align: center;
}
.contenshow {
  width: 100vw;
  height: 60vh;
  text-align: center;
}
</style>
