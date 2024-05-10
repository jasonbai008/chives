<template>
  <div id="app">
    <HelloWorld />
    <iframe src="https://wallstreetcn.com/c/chart?description=%E4%B8%AD%E5%9B%BD10%E5%B9%B4%E6%9C%9F%E5%9B%BD%E5%80%BA%E6%94%B6%E7%9B%8A%E7%8E%87&interval=1D&symbol=CN10YR.OTC" frameborder="0" v-show="isShowIframe"></iframe>
  </div>
</template>

<script>
import HelloWorld from "./components/HelloWorld.vue";

export default {
  name: "App",
  components: {
    HelloWorld,
  },
  data() {
    return {
      isShowIframe: false,
    };
  } 
};
</script>

<style lang="scss">
body {
  margin: 0;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  max-width: 640px;
  margin: 0 auto;
  position: relative;
}
iframe {
  display: block;
  width: 100vh;
  height: 100vw;
  position: absolute;
  transform: rotate(90deg);
  transform-origin: 0 0 0;
  left: 100vw;
  top: 0;
}
</style>
