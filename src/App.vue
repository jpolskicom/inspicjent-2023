<template>
  <div id="app">
    <HelloWorld />
  </div>
</template>

<script>
import HelloWorld from "./components/HelloWorld.vue";

export default {
  name: "App",
  components: {
    HelloWorld,
  },
};
</script>

<style>
html,
body {
  margin: 0;
  padding: 0;
  background: #222;
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
}
*,
*::after,
*::before {
  box-sizing: border-box;
}
::-webkit-scrollbar {
  width: 7px;
  background-color: #000;
}

::-webkit-scrollbar-thumb {
  background-color: #444;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #ffffff;
  background: #000;
  width: 1024px;
  height: 768px;
}
</style>
