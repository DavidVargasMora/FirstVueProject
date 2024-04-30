<script>
import SplashScreen from "@/components/SplashScreen.vue"
import { defineAsyncComponent } from "vue";
import Layout from "./components/Layout.vue";

export default {
  components: {
    SplashScreen,
    Home: defineAsyncComponent(() =>
      new Promise ((resolve) => {
        setTimeout(() => {
          resolve(import("./components/Home.vue"))
        }, 2500);
      })
    ),
  }
}
</script>

<template>
    <Suspense>
      <template #default>
        <Home></Home>
      </template>
      <template #fallback>
        <SplashScreen></SplashScreen>
      </template>
    </Suspense>
</template>

<style scoped>
  html,
  body,
  .app {
    min-height: 100vh;
    margin: 0;
    font-family: Arial, Helvetica, sans-serif;
  }
</style>
