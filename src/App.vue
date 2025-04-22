<template>
  <div id="app">
    <h1>图片上传应用</h1>
    <ImageUploader />
  </div>
</template>

<script>
import ImageUploader from './ImageUploader.vue'

export default {
  name: 'App',
  components: {
    ImageUploader
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>