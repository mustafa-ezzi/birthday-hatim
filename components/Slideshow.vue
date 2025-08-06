<template>
  <div class="slideshow-container fade-in">
    <img :src="images[currentIndex]" alt="slide" class="slide-image" />
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'

const images = [
  '/background.png',
  '/cake.jpg',
  '/stars.jpg',
//   '/img4.jpg'
]

const currentIndex = ref(0)

onMounted(() => {
  setInterval(() => {
    currentIndex.value = (currentIndex.value + 1) % images.length
  }, 3000)
})
</script>

<style scoped>
.slideshow-container {
  margin-top: 2rem;
}

.slide-image {
  width: 80%;
  max-width: 600px;
  border-radius: 20px;
  box-shadow: 0 0 20px rgba(0,0,0,0.2);
  transition: transform 1s ease-in-out;
  animation: fadeIn 1s ease-in-out;
}
</style>
