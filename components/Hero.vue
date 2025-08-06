<template>
  <div class="hero fade-in">
    <h1>🎉 Happy Birthday, <span>Ummehaani</span> 🎂</h1>
    <p>Wishing you joy, love, and everything you’ve ever dreamed of.</p>
    <button @click="$emit('showSurprise')">Click For a Surprise 🎁</button>
  </div>
</template>

<style scoped>
.hero {
  margin-top: 120px;
  padding: 2rem;
}

h1 {
  font-size: 3.5rem;
  color: #5B9BD5;
}

h1 span {
  color: #3A7CC1;
}

p {
  font-size: 1.5rem;
  color: #333;
  margin: 1rem 0 2rem;
}

button {
  padding: 14px 32px;
  background: #5B9BD5;
  color: white;
  border: none;
  border-radius: 30px;
  font-size: 1.2rem;
  cursor: pointer;
  transition: all 0.3s;
}

button:hover {
  background: #3A7CC1;
}
</style>
