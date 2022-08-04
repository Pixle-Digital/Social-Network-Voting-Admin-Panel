<template>
  <div id="app-container" :class="getMenuType">
    <nav-bar />
    <side-bar />
    <main>
      <div class="container-fluid">
        <router-view />
      </div>
    </main>
  </div>
</template>

<script>
import navBar from "../../components/navBar.vue";
import sideBar from "../../components/sideBar.vue";
import { mapGetters } from "vuex";

export default {
  components: {
    navBar,
    sideBar,
    show: true,
  },
  computed: {
    ...mapGetters(["getMenuType"]),
  },
  mounted() {
    setTimeout(() => {
      document.body.classList.add("default-transition");
    }, 100);
  },
};
</script>

<style>
</style>
