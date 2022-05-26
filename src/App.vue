<template>

  <div class="bk">
    <router-view></router-view>
  </div>

</template>

<script>
  export default {
    name: 'App',
  }
</script>


<style lang="scss">

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;

}

.Routerview{
  z-index: 2;
}

nav {
  z-index: 2;
  padding: 30px;

  a {
    font-weight: bold;
    color: #2c3e50;

    &.router-link-exact-active {
      color: #42b983;
    }
  }
}


/* CSS which you need for blurred box */
.bk{
  max-width: 100vw;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  -webkit-box-align: stretch;
  align-items: stretch;
  position: relative;
  overflow: hidden;
}


</style>
