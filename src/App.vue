<template>
  <b-container id="app">
      <router-view/>
  </b-container>
</template>

<style>

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;

  min-height: 100vh;
  max-height: 100vh;
  min-width: 100%;
  max-width: 100%;

  padding: 0px;

  color: #ffffff;
  background-color: #46859b;

  display: flex;
  flex-direction: column;
}

</style>
