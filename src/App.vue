<template>
  <div id="app">
    <main>
      <router-view />
    </main>
    <footer>
      Powered by
      <a href="https://github.com/gwy15/wechat-push">
        Wechat-Push
      </a>
      © 2020
      <a href="https://gwy15.com">
        gwy15
      </a>
    </footer>
  </div>
</template>

<style lang="less" scoped>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  font-size: 16px;

  min-height: 100vh;
  width: 100vw;

  main {
    min-height: calc(100vh - 30px);
    padding: 20px;
  }

  footer {
    width: 100vw;
    height: 30px;
    font-size: 14px;
  }
}
</style>
