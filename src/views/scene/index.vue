<template xmlns="http://www.w3.org/1999/html">
  <div>
    <div class="video-container">
      <video controls autoplay>
        <source :src="videoPath" type="video/mp4">
      </video>
    </div>
    <iframe
      ref="htmlRef"
      src=""
      class="markdown-container"
      scrolling="no"
    />
  </div>
</template>

<script>

export default {
  props: ['htmlName', 'videoName'],
  data() {
    return {
      videoPath: './videoFile/scene1.mp4'
    }
  },
  created() {
    this.videoPath = './videoFile/' + this.videoName + '.mp4'
  },
  mounted() {
    this.$refs.htmlRef.src = this.htmlName + '.html'
  }
}
</script>

<style scoped>
.markdown-container{
  width: 100%;
  height: 1200px;
  border-color: rgba(0, 0, 0, 0);
  overflow: hidden;
}
.video-container {
  width: 60%;
  margin: 0 auto;
  border: none;
  mix-blend-mode: darken;
}
.video-container:focus{
  outline: none;
}
video {
  width: 100%;
  display: block;
  border: none;
  mix-blend-mode: darken;
}
video:focus{
  outline: none;
}
</style>
