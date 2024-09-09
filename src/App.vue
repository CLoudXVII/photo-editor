<template>
  <PhotoShop/>
</template>

<script>
import PhotoShop from './components/PhotoShop.vue'

export default {
  name: 'App',
  components: {
    PhotoShop
  }
}
</script>

<style>
@import url(./global.css);
@import url(./assets/fonts/fonts.css);

body {
  font-family: "Montserrat", sans-serif;
  background-color: lightgray;
}

#app {
  block-size: 100vb;
}

</style>
