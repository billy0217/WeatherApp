<template>
  <router-view/>
</template>

<style lang="scss">
  $black: #000;
  $white: #fff;
  $purple: #925ce9;
  $blue: #33b7d5;

  * {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
  }

  html,
  body {
    margin: 0 auto;
    padding: 0;
  }

  body {
    font-family: 'montserrat', sans-serif;
  }

  #app {
    transition: .4s;
  }

  .l-page {
    min-height: 100vh;
    width: 100%;
    padding: 25px;

    background-image: linear-gradient(to bottom, rgba($purple, 1), rgba($blue, 1));
  }
</style>
