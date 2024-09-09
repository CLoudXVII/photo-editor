<template>
  <PhotoEditor/>
</template>

<script>
import PhotoEditor from './components/PhotoEditor.vue'

export default {
  name: 'App',
  components: {
    PhotoEditor
  }
}
</script>

<style>
@import url(./global.css);
@import url(./assets/fonts/fonts.css);

#app {
  block-size: 100vb;
}

body {
  font-family: "Montserrat", sans-serif;
  background-color: lightgray;
}

</style>
