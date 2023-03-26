<template>
  <div class="container">
    <div class="content-upload">
      <h1>Subida de Archivos usando Nuxt 2</h1>
      <UploadS3Component />
    </div>
  </div>
</template>
<script>
import UploadS3Component from '~/components/UploadS3Component.vue';

export default {
    name: "IndexPage",
    components: { UploadS3Component }
}
</script>

<style>
body {
  background: #333;
  text-align: center;
  color: #eee;
  font-family: system-ui, sans-serif;
}

h1 {
  font-size: 42px;
  font-weight: 600;
  margin: 0 0 30px;
}

pre {
  background: #000;
  text-align: left;
  padding: 20px;
  margin: 0 auto 30px;
}

* {
  box-sizing: border-box;
}
.container {
  width: 100%;
  height: 100vh;
  text-align: center;
}
.content-upload {
  margin-top: 100px;
}
</style>