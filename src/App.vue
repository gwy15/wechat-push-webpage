<template>
  <div id="app">
    <!-- <header>
      Wechat-Push
    </header> -->
    <main>
      <router-view />
    </main>
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
  min-width: 100vw;

  header {
    height: 60px;
    font-size: 36px;
    padding: 10px;
  }

  main {
    height: calc(100vh - 60px);
    padding: 20px;
  }
}
</style>
