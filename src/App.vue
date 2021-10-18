<template>
  <div id="app">
      <div id="nav">
        <router-link class="nav_first" to="/">Converter</router-link>  |
        <router-link class="nav_second" to="/about">Portfolio</router-link>
      </div>
      <router-view/>
  </div>
</template>



<style lang="scss">
* {
  box-sizing: border-box;
  margin: 0;
  transition: 1s;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin: 0;
  width: 100%;
  height: 100%;
}
.container {
  display: flex;
  flex-direction: column;
  margin: 0 auto;
  width: 75%;
}
h1 {
  font-size: 30px;
  text-transform: uppercase;
  letter-spacing: 4px;
  color: #A87F6D;
}
#nav {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 70px;
  box-shadow: inset 0px -1px 1px 0px #ede5e1;
  a {
    font-weight: bold;
    color: #000000;
    &:hover {
      color: #a86d79;
    }
    &.router-link-exact-active {
      color: #754c54;
    }
  }
}
.nav_first {
  margin-right: 5px;
}
.nav_second {
  margin-left: 5px;
}
</style>
