<template>
  <div class="video-bg">
    <video autoplay muted loop playsinline>
      <source src="/birthday-video.mp4" type="video/mp4" />
    </video>
    <div class="overlay"></div>
  </div>
</template>

<style scoped>
.video-bg {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: -1;
  overflow: hidden;
}

video {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.overlay {
  position: absolute;
  inset: 0;
  background-color: rgba(255, 255, 255, 0.8);
}
</style>
