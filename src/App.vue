<template>
  <b-container id="app">
      <h1 class="title p-3 m-0"> BOARD BOOK </h1>
    <b-row>
      <router-view/>
    </b-row>
  </b-container>
</template>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2D2D2D;
  min-height: 100vh;

  display: flex;
  flex-direction: column;
}

#nav {
  padding: 0px;
}

#nav a {
  font-weight: bold;
}

#nav a.router-link-exact-active {
  color: #2D2D2D;
}

.title {
  font-size: 100px;
  font-weight: bold;
  text-align: center;
  width: 100%;
}
</style>
