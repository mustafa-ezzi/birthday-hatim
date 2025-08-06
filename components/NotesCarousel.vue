<template>
  <div class="notes-carousel fade-in">
    <div class="note-card" 
         v-for="(note, index) in notes" 
         :key="index"
         @mouseenter="showNote(index)"
         @mouseleave="hideNote(index)"
         :class="{ active: currentNote === index }">
      <p class="note-text">{{ note }}</p>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const notes = [
  "You're more than a friend – you're family ❤️",
  "You light up every room you walk into 🌟",
  "The world is better with you in it 💫",
  "Today is yours. Shine like you always do 💙"
]

const currentNote = ref(0)

function showNote(index) {
  currentNote.value = index
}

function hideNote(index) {
  // Optional: reset to first note when not hovering
  if (currentNote.value === index) {
    currentNote.value = 0
  }
}
</script>

<style scoped>
.notes-carousel {
  margin-top: 3rem;
  padding: 0 1.5rem;
  max-width: 600px;
  margin-left: auto;
  margin-right: auto;
  animation: fadeIn 1s ease-in-out;
  display: flex;
  flex-wrap: wrap;
  gap: 1rem;
  justify-content: center;
}

.note-card {
  background: linear-gradient(145deg, #fdf2f8, #fce7f3);
  border: 2px solid #f472b6;
  border-radius: 15px;
  padding: 1.5rem;
  width: 280px;
  min-height: 120px;
  cursor: pointer;
  transition: all 0.3s ease;
  transform: scale(0.95);
  opacity: 0.7;
  box-shadow: 0 4px 15px rgba(236, 72, 153, 0.2);
}

.note-card:hover,
.note-card.active {
  transform: scale(1.05) rotateY(5deg);
  opacity: 1;
  box-shadow: 0 8px 25px rgba(168, 85, 247, 0.3);
  background: linear-gradient(145deg, #ffffff, #fce7f3);
  border-color: #ec4899;
}

.note-text {
  color: #3A7CC1;
  font-style: italic;
  font-size: 1.2rem;
  text-align: center;
  margin: 0;
  transition: color 0.3s ease;
}

.note-card:hover .note-text,
.note-card.active .note-text {
  color: #831843;
  font-weight: 500;
}

/* Mobile responsive styles */
@media (max-width: 768px) {
  .notes-carousel {
    padding: 0 0.5rem;
    gap: 0.8rem;
    grid-template-columns: 1fr;
    max-width: 100%;
  }

  .note-card {
    width: 100%;
    max-width: 250px;
    padding: 0.8rem;
    min-height: 80px;
  }

  .note-text {
    font-size: 0.9rem;
    line-height: 1.3;
  }
}

@media (max-width: 480px) {
  .notes-carousel {
    padding: 0 0.3rem;
    gap: 0.6rem;
  }

  .note-card {
    max-width: 220px;
    padding: 0.6rem;
    min-height: 70px;
  }

  .note-text {
    font-size: 0.85rem;
    line-height: 1.2;
  }
}
</style>
