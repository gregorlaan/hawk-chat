<template>
  <div id="app">
    <router-view/>
  </div>
</template>

<script>
export default {
  name: 'App'
}
</script>

<style>
html, body {
  margin: 0;
}

#app {
  font-family: -apple-system,BlinkMacSystemFont,"Segoe UI",Helvetica,Arial,sans-serif,"Apple Color Emoji","Segoe UI Emoji","Segoe UI Symbol";
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
  background: linear-gradient(top, #7A5FFF 0%, #2083f3 50%, #01d0ff 100%);
  width: 300px;
  margin: 0 auto;
  overflow-y: scroll;
}

#app > div {
  max-width: 300px;
  width: 100%;
}
</style>
