<template>
  <div class="modal" v-if="visible">
    <div class="modal-content fade-in">
      <span class="close" @click="$emit('close')">×</span>
      <h2>💌 From Someone Who Cares Deeply 💌</h2>
      <p>
        Ummehaani, you are appreciated more than you know.
        You bring happiness, peace, and laughter to those around you.
        May today remind you how deeply loved you are 💙
      </p>
    </div>
  </div>
</template>

<script setup>
defineProps({ visible: Boolean })
</script>

<style scoped>
.modal {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.6);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 999;
}

.modal-content {
  background: white;
  padding: 2rem;
  border-radius: 16px;
  max-width: 500px;
  text-align: center;
  box-shadow: 0 0 30px rgba(0, 0, 0, 0.4);
  position: relative;
}

.close {
  position: absolute;
  top: 1rem;
  right: 1.5rem;
  font-size: 2rem;
  cursor: pointer;
  color: #aaa;
}
</style>
