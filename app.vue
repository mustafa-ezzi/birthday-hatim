<!-- BirthdayApp.vue -->
<template>
  <div class="container" @mousemove="createHeart" @click="createSparkle">
    <!-- 🎵 Background Music -->
    <audio autoplay loop ref="audio" :src="songUrl" style="display: none" />
    <audio loop ref="wishAudio" :src="wishSongUrl" style="display: none" />
    
    <!-- Music Control Button -->
    <div class="music-control">
      <button @click="toggleMusic" class="music-btn" :class="{ playing: isPlaying }">
        {{ isPlaying ? '🔊' : '🔇' }}
      </button>
    </div>

    <!-- ✨ Floating Stars -->
    <div class="floating-stars">
      <span v-for="n in 15" :key="n" class="star">✨</span>
    </div>

    <!-- SECTION 1: Intro -->
    <section class="section fade-in" v-if="section === 1">
      <h1>🎉 Happy Birthday <span class="name">Ummehaani</span> 🎂</h1>
      <p class="note typewriter">Here's a magical birthday journey 💙</p>
      <NotesCarousel />
      <button class="animated-btn" @click="next">Start the Journey</button>
    </section>

    <!-- SECTION 2: Polaroid Gallery -->
    <section class="section " v-if="section === 2">
      <h2>📸 Memories in Polaroids</h2>
      <div class="polaroid-gallery">
        <div v-for="(img, i) in images" :key="i" class="polaroid">
          <img :src="img" />
          <p>{{ imageCaptions[i] }}</p>
        </div>
      </div>
      <button class="animated-btn" @click="next">Next ✨</button>
    </section>

    <!-- SECTION 2.5: AI Poem Generator -->
    <section class="section fade-in" v-if="section === 3">
      <h2>🤖✨ AI Love Poem Generator</h2>
      <div class="ai-generator">
        <p class="ai-description">Let our AI create a personalized poem just for you! 💕</p>

        <div class="input-group">
          <input v-model="aiPrompt" placeholder="Enter a word that describes Ummehaani..." class="ai-input" />
          <button class="animated-btn" @click="generatePoem" :disabled="isGenerating">
            {{ isGenerating ? '🤖 Generating...' : '✨ Generate Poem' }}
          </button>
        </div>

        <div v-if="generatedPoem" class="ai-output">
          <div class="typing-container">
            <h3>🎭 Your AI-Generated Poem:</h3>
            <div class="poem-text" :class="{ typing: isTyping }">
              {{ displayedPoem }}
            </div>
          </div>
          <button class="animated-btn" @click="generateAnother">Generate Another ✨</button>
        </div>

        <div v-if="!generatedPoem" class="ai-suggestions">
          <p>💡 Try words like: amazing, kind, funny, brilliant, caring...</p>
        </div>
      </div>

      <button class="animated-btn" @click="next" v-if="generatedPoem">Continue Journey 🚀</button>
    </section>

    <!-- SECTION 3: Trivia Game -->
    <section class="section fade-in" v-if="section === 4">
      <h2>🧠 Trivia Time!</h2>
      <div class="quiz">
        <p>{{ trivia[currentTrivia].question }}</p>
        <div class="options">
          <button v-for="(option, i) in trivia[currentTrivia].options" :key="i" @click="checkAnswer(option)">
            {{ option }}
          </button>
        </div>
        <p v-if="triviaAnswered" class="trivia-result">{{ triviaResult }}</p>
      </div>
    </section>

    <!-- SECTION 4: Balloon Pop -->
    <section class="section fade-in" v-if="section === 5">
      <h2>🎈 Pop the Balloons!</h2>
      <div class="balloon-game">
        <div v-for="b in poppedBalloons" :key="b.id" class="balloon-pop" :class="{ popped: b.popped }"
          @click="popBalloon(b.id)">
          🎈
        </div>
      </div>
      <button class="animated-btn" v-if="allBalloonsPopped" @click="next">
        All Popped! 🎉
      </button>
    </section>

    <!-- SECTION 5: Wish Maker -->
    <section class="section fade-in" v-if="section === 6">
      <h2>🌠 Make a Wish</h2>
      
      <!-- Cute Video Section -->
      <div class="video-memory">
        <h3>💐 A Sweet Memory</h3>
        <div class="video-container">
          <video controls autoplay muted loop class="cute-video">
            <source src="/flower-video.mp4" type="video/mp4" />
            Your browser does not support the video tag.
          </video>
          <p class="video-caption">Applying flowers on your beautiful hands 🌸💕</p>
        </div>
      </div>
      
      <input v-model="wish" placeholder="Type your birthday wish..." class="wish-input" />
      <button class="animated-btn" @click="submitWish">Send to Stars 🌌</button>
      <p v-if="wishMade" class="wish-confirm">Wish Sent! Check the stars 🌟</p>
      <button class="animated-btn replay" @click="next">💖 Final Surprise</button>
    </section>

    <!-- SECTION 6: Final Surprise -->
    <section class="section final-scene" :class="{ lit: lightsOn }" v-if="section === 7">
      <h2 class="final-title">💌 A Love Note...</h2>

      <div class="final-content">
        <p class="final-message">
          To the most amazing soul I've ever met,<br />
          Every moment with you is magic, and this is just the beginning.<br />
          May this birthday be the most beautiful chapter yet 💙<br /><br />
          — With endless love, Hatim 💫
          <br /><br />

        </p>

        <button v-if="!lightsOn" class="glow-btn" @click="toggleLights">
          💡 Lights On
        </button>


        <button v-if="lightsOn" class="animated-btn mt-4" @click="restart">
          🔁 Replay the Journey
        </button>
      </div>
    </section>

  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'
import confetti from 'canvas-confetti'
import NotesCarousel from '~/components/NotesCarousel.vue'

const section = ref(1)
const audio = ref(null)
const wishAudio = ref(null)
const songUrl = '/perfect.mp3'
const wishSongUrl = '/birthday.mp3'
const isPlaying = ref(false)
const isWishPlaying = ref(false)

// Music controls
function toggleMusic() {
  if (audio.value) {
    if (isPlaying.value) {
      audio.value.pause()
      isPlaying.value = false
    } else {
      audio.value.play()
      isPlaying.value = true
    }
  }
}

function startMusic() {
  if (audio.value) {
    audio.value.play().then(() => {
      isPlaying.value = true
    }).catch(() => {
      isPlaying.value = false
    })
  }
}

function startWishMusic() {
  // Stop background music
  if (audio.value && isPlaying.value) {
    audio.value.pause()
    isPlaying.value = false
  }
  
  // Start wish song
  if (wishAudio.value) {
    wishAudio.value.play().then(() => {
      isWishPlaying.value = true
    }).catch(() => {
      isWishPlaying.value = false
    })
  }
}

function stopWishMusic() {
  if (wishAudio.value && isWishPlaying.value) {
    wishAudio.value.pause()
    isWishPlaying.value = false
  }
  
  // Resume background music
  if (audio.value) {
    audio.value.play()
    isPlaying.value = true
  }
}

const voiceNotes = [
  {
    src: '/voiceNote.opus',
    caption: '💖 A heartfelt birthday wish — the kind that melts your heart.'
  },
  {
    src: '/voiceNote2.opus',
    caption: '😏 And here comes the sarcasm... but in the cutest way possible!'
  }
]

// Images
const images = [
  '/img1.jpg', '/img2.jpg', '/img3.jpg', '/img4.jpg',
  '/img5.jpg', '/img6.jpg', '/img7.jpg', '/img8.jpg', '/img9.jpg'
]
const imageCaptions = [
  'That one blessed night❤😘#Engagement🫶🏻', 'My favorite love language?𝐘𝐎𝐔❤😘', 'That 12AM Surprise By You🥰😘#BirthdayMemory', 'Dressed to impress… each other 😘💫',
  'Matching Vibes > Matching Outfits ✨👫', 'Our First Eid Together🤟🏻😍', 'Cutest reflection in the mirror? Us.♾ 😍🪞', 'They looked for the moon, but found us instead. 😉🌚🤭', 'Shared, dreamed, and built a bond that only grows.🫶🏻💋#6MonthsAnniversarykiyaad♥',
  'From the memories of your Coconut Birthday😍'
]

const lightsOn = ref(false)
function toggleLights() {
  lightsOn.value = true
  console.log('Lights turned on!')

}

// Trivia
const trivia = [
  {
    question: 'Where did we first talk?',
    options: ['Instagram', 'Snapchat', 'WhatsApp'],
    answer: 'Instagram'
  },
  {
    question: 'Your favorite color?',
    options: ['Blue 💙', 'Black', 'Purple'],
    answer: 'Black'
  },
  {
    question: 'Do I prefer movies, series or songs?',
    options: ['Movies', 'Series', 'Songs'],
    answer: 'Movies'
  },
  {
    question: 'What’s my dream vacation destination?',
    options: ['Maldives', 'Bali ', 'Malaysia'],
    answer: 'Maldives'
  }
  ,
  {
    question: 'How do you see our future together?',
    options: ['Allah khair krey 😂😂😂😂', 'Will have a good future IA🥹♥ ', 'Will be very happy being with you💕'],
    answer: 'Allah khair krey 😂😂😂😂'
  }
  ,
  {
    question: "What's your favorite memory of us together?",
    options: ['This has to be answered personally  '],
    answer: 'This has to be answered personally'
  }
]
const currentTrivia = ref(0)
const triviaAnswered = ref(false)
const triviaResult = ref('')

function checkAnswer(option) {
  if (option === trivia[currentTrivia.value].answer) {
    triviaResult.value = 'Correct! 🧠💖'
  } else {
    triviaResult.value = 'Oops! Try the next one 😊'
  }
  triviaAnswered.value = true
  setTimeout(() => {
    currentTrivia.value++
    triviaAnswered.value = false
    triviaResult.value = ''
    if (currentTrivia.value >= trivia.length) next()
  }, 2000)
}

// Balloons
const poppedBalloons = ref(generateBalloons())
function generateBalloons() {
  return Array.from({ length: 10 }, (_, i) => ({ id: i, popped: false }))
}
function popBalloon(id) {
  const balloon = poppedBalloons.value.find(b => b.id === id)
  if (balloon && !balloon.popped) {
    balloon.popped = true
  }
}
const allBalloonsPopped = computed(() =>
  poppedBalloons.value.every(b => b.popped)
)

// Wish
const wish = ref('')
const wishMade = ref(false)
function submitWish() {
  if (wish.value.trim() !== '') {
    wishMade.value = true
    fireworkShow()
  }
}

// Sparkles & Hearts
function createHeart(e) {
  const heart = document.createElement('div')
  heart.className = 'heart'
  heart.innerText = '❤'
  heart.style.left = `${e.clientX}px`
  heart.style.top = `${e.clientY}px`
  document.body.appendChild(heart)
  setTimeout(() => heart.remove(), 1000)
}
function createSparkle(e) {
  const sparkle = document.createElement('div')
  sparkle.className = 'sparkle'
  sparkle.innerHTML = `<svg width="20" height="20" viewBox="0 0 24 24">
    <path fill="#3B82F6" d="M12 2l2 5h5l-4 3.5L17 16l-5-3-5 3 2-5.5L5 7h5z"/>
  </svg>`
  sparkle.style.left = `${e.clientX}px`
  sparkle.style.top = `${e.clientY}px`
  document.body.appendChild(sparkle)
  setTimeout(() => sparkle.remove(), 1000)
}

// Confetti
function fireworkShow() {
  confetti({ particleCount: 180, spread: 100, origin: { y: 0.6 } })
  confetti({ particleCount: 80, angle: 60, origin: { x: 0, y: 0.5 } })
  confetti({ particleCount: 80, angle: 120, origin: { x: 1, y: 0.5 } })
}

// Navigation
function next() {
  if (section.value === 5) {
    // Starting wish section - play wish song
    startWishMusic()
  } else if (section.value === 6) {
    // Leaving wish section - stop wish song
    stopWishMusic()
  }
  section.value++
}

function restart() {
  section.value = 1
  poppedBalloons.value = generateBalloons()
  currentTrivia.value = 0
  triviaAnswered.value = false
  triviaResult.value = ''
  wish.value = ''
  wishMade.value = false
  stopWishMusic()
  fireworkShow()
}

// AI Poem Generator
const aiPrompt = ref('')
const generatedPoem = ref('')
const displayedPoem = ref('')
const isGenerating = ref(false)
const isTyping = ref(false)

const poemTemplates = [
  {
    template: "In a world so vast and wide,\nThere's someone {adjective} by my side.\nUmmehaani, with a heart so true,\nEvery day feels bright and new.\n\nYour {adjective} spirit lights the way,\nMaking magical every single day.\nHappy birthday to someone so dear,\nMay joy and love follow you all year! 🎂✨",
    adjectives: ["amazing", "wonderful", "brilliant", "fantastic", "incredible"]
  },
  {
    template: "Roses are red, violets are blue,\nUmmehaani is {adjective}, and that's so true!\nWith a smile that can light up any room,\nYou chase away every hint of gloom.\n\nOn this special day we celebrate you,\nFor being {adjective} in all that you do.\nMay your birthday be filled with delight,\nAnd your future be wonderfully bright! 🌹💙",
    adjectives: ["caring", "loving", "kind", "sweet", "thoughtful"]
  },
  {
    template: "There once was a person named Ummehaani so {adjective},\nWhose birthday today is quite fantastic!\nWith laughter and cheer,\nWe celebrate here,\nA soul that's truly {adjective}! 🎉\n\nMay this year bring adventures new,\nAnd dreams that beautifully come true.\nHappy birthday to someone so bright,\nYou make everything feel just right! ✨",
    adjectives: ["bright", "smart", "wise", "clever", "brilliant"]
  }
]

function generatePoem() {
  if (!aiPrompt.value.trim()) return

  isGenerating.value = true
  generatedPoem.value = ''
  displayedPoem.value = ''

  // Simulate AI thinking time
  setTimeout(() => {
    const randomTemplate = poemTemplates[Math.floor(Math.random() * poemTemplates.length)]
    const userWord = aiPrompt.value.trim().toLowerCase()

    // Use user's word or fallback to template suggestions
    const adjective = randomTemplate.adjectives.includes(userWord)
      ? userWord
      : randomTemplate.adjectives[Math.floor(Math.random() * randomTemplate.adjectives.length)]

    generatedPoem.value = randomTemplate.template.replace(/{adjective}/g, adjective)
    isGenerating.value = false
    typePoem()
  }, 2000)
}

function typePoem() {
  isTyping.value = true
  let i = 0
  const text = generatedPoem.value

  const typeInterval = setInterval(() => {
    displayedPoem.value = text.slice(0, i)
    i++

    if (i > text.length) {
      clearInterval(typeInterval)
      isTyping.value = false
    }
  }, 50)
}

function generateAnother() {
  generatedPoem.value = ''
  displayedPoem.value = ''
  aiPrompt.value = ''
}

// Mount
onMounted(() => {
  startMusic()
  document.title = 'Happy Birthday, Ummehaani 🎂'
})
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Caveat:wght@500&display=swap');

:root {
  --pink-light: #fce7f3;
  --pink-mid: #f472b6;
  --pink: #ec4899;
  --pink-dark: #be185d;
  --pink-darker: #831843;
  --purple: #a855f7;
  --purple-dark: #7c3aed;
}

/* Layout */
.container {
  max-width: 860px;
  margin: auto;
  padding: 2rem;
  position: relative;
  background: linear-gradient(135deg, rgba(252, 231, 243, 0.95), rgba(255, 255, 255, 0.9));
  border-radius: 20px;
  box-shadow: 0 0 30px rgba(236, 72, 153, 0.3);
  backdrop-filter: blur(10px);
}

/* Typography */
h1,
h2 {
  font-family: 'Caveat', cursive;
  font-size: 2.6rem;
  background: linear-gradient(135deg, var(--pink-darker), var(--purple-dark));
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}

p {
  color: #374151;
  font-size: 1.1rem;
  line-height: 1.6;
  margin: 0.5rem 0 1rem;
}

/* Buttons */
.animated-btn {
  background: linear-gradient(135deg, var(--pink-mid), var(--purple));
  color: white;
  padding: 14px 28px;
  border-radius: 30px;
  margin-top: 10px;
  font-size: 1rem;
  border: none;
  cursor: pointer;
  box-shadow: 0 4px 20px rgba(236, 72, 153, 0.4);
  transition: 0.3s ease;
  position: relative;
  overflow: hidden;
}

.animated-btn::before {
  content: '';
  position: absolute;
  top: 0;
  left: -100%;
  width: 100%;
  height: 100%;
  background: linear-gradient(90deg, transparent, rgba(255, 255, 255, 0.3), transparent);
  transition: 0.5s;
}

.animated-btn:hover::before {
  left: 100%;
}

.animated-btn:hover {
  transform: scale(1.05);
  box-shadow: 0 6px 25px rgba(168, 85, 247, 0.5);
}

/* Sections */
.section {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  padding: 2rem 1rem;
  animation: fadeIn 1s ease;
}

@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(30px);
  }

  to {
    opacity: 1;
    transform: translateY(0);
  }
}

/* Typewriter */
.typewriter {
  overflow: hidden;
  border-right: 2px solid var(--pink);
  white-space: nowrap;
  margin: 1rem auto;
  animation: typing 3s steps(40, end), blink-caret 0.75s step-end infinite;
}

@keyframes typing {
  from {
    width: 0
  }

  to {
    width: 100%
  }
}

@keyframes blink-caret {

  from,
  to {
    border-color: transparent
  }

  50% {
    border-color: var(--pink)
  }
}

/* Polaroid */
.polaroid-gallery {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  gap: 1.2rem;
  padding: 1rem;
}

.polaroid {
  background: linear-gradient(145deg, #ffffff, #fce7f3);
  border: 2px solid var(--pink-light);
  padding: 8px;
  width: 180px;
  box-shadow: 0 6px 20px rgba(236, 72, 153, 0.2);
  border-radius: 12px;
  transition: transform 0.3s ease;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.polaroid:hover {
  transform: scale(1.05) rotate(1deg);
  box-shadow: 0 8px 25px rgba(168, 85, 247, 0.3);
}

.polaroid img {
  width: 100%;
  height: auto;
  border-radius: 8px;
  object-fit: cover;
}

.polaroid p {
  margin-top: 0.5rem;
  font-size: 0.9rem;
  text-align: center;
  color: var(--pink-darker);
  font-style: italic;
  line-height: 1.4;
}

/* Voice Memories */
.voice-memories {
  padding-top: 2rem;
}

.voice-title {
  font-size: 2.4rem;
  font-family: 'Caveat', cursive;
  background: linear-gradient(135deg, var(--pink-darker), var(--purple-dark));
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
  margin-bottom: 0.5rem;
}

.voice-grid {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  gap: 1.5rem;
  padding: 1rem;
  margin-bottom: 2rem;
}

.voice-card {
  background: linear-gradient(145deg, #fdf2f8, #fce7f3);
  border: 2px solid var(--pink-light);
  border-radius: 16px;
  padding: 1.2rem;
  width: 280px;
  box-shadow: 0 4px 15px rgba(236, 72, 153, 0.2);
  display: flex;
  flex-direction: column;
  align-items: center;
  transition: transform 0.3s ease, box-shadow 0.3s ease;
}

.voice-card:hover {
  transform: translateY(-5px);
  box-shadow: 0 6px 20px rgba(168, 85, 247, 0.3);
}

.voice-icon {
  font-size: 1.8rem;
  color: var(--pink-dark);
}

.voice-caption {
  font-size: 1rem;
  color: #374151;
  font-weight: 500;
  font-style: italic;
}

/* Trivia */
.options button {
  background: linear-gradient(135deg, var(--pink-light), #fdf2f8);
  color: var(--pink-darker);
  border: 2px solid var(--pink-mid);
  padding: 10px 20px;
  border-radius: 20px;
  margin: 0.4rem 0;
  cursor: pointer;
  transition: all 0.3s ease;
}

.options button:hover {
  background: linear-gradient(135deg, var(--pink-mid), var(--purple));
  color: white;
  transform: translateY(-2px);
}

.trivia-result {
  font-weight: bold;
  margin-top: 1rem;
  color: var(--pink-darker);
}

/* Balloons */
.balloon-game {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  gap: 1rem;
  padding: 2rem;
}

.balloon-pop {
  font-size: 2.5rem;
  cursor: pointer;
  user-select: none;
  transition: transform 0.3s ease, opacity 0.3s ease;
  filter: hue-rotate(300deg);
}

.balloon-pop.popped {
  transform: scale(0.2);
  opacity: 0;
  pointer-events: none;
}

.balloon-pop:nth-child(3n) {
  color: var(--pink);
}

.balloon-pop:nth-child(4n) {
  color: var(--purple);
}

/* Video Memory Styles */
.video-memory {
  margin: 2rem 0;
  padding: 1.5rem;
  background: linear-gradient(145deg, rgba(255, 255, 255, 0.9), rgba(252, 231, 243, 0.8));
  border-radius: 20px;
  box-shadow: 0 8px 25px rgba(236, 72, 153, 0.2);
  border: 2px solid var(--pink-light);
}

.video-memory h3 {
  color: var(--pink-dark);
  font-family: 'Caveat', cursive;
  font-size: 1.8rem;
  margin-bottom: 1rem;
}

.video-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 1rem;
}

.cute-video {
  width: 100%;
  max-width: 400px;
  border-radius: 15px;
  box-shadow: 0 6px 20px rgba(236, 72, 153, 0.3);
  border: 3px solid var(--pink-light);
  transition: transform 0.3s ease, box-shadow 0.3s ease;
}

.cute-video:hover {
  transform: scale(1.02);
  box-shadow: 0 8px 25px rgba(168, 85, 247, 0.4);
}

.video-caption {
  font-style: italic;
  color: var(--pink-darker);
  font-size: 1.1rem;
  text-align: center;
  margin: 0;
  font-family: 'Caveat', cursive;
}

/* Wish Maker */
input.wish-input {
  padding: 0.6rem 1rem;
  font-size: 1.1rem;
  border: 2px solid var(--pink-dark);
  border-radius: 30px;
  width: 80%;
  max-width: 400px;
  text-align: center;
  background: linear-gradient(135deg, #fdf2f8, #ffffff);
  margin-top: 1rem;
}

.wish-confirm {
  margin-top: 1rem;
  color: var(--pink);
  font-weight: 500;
}

/* Final Scene */
.final-scene {
  background: linear-gradient(135deg, #1f1f23, #2d1b69);
  color: #94a3b8;
  transition: background 1s ease, color 1s ease;
}

.final-scene.lit {
  background: linear-gradient(135deg, var(--pink-light), #fdf2f8);
  color: var(--pink-darker);
}

.final-scene .glow-btn {
  background: linear-gradient(135deg, var(--pink), var(--purple));
  color: white;
  padding: 14px 28px;
  border: none;
  border-radius: 50px;
  font-size: 1.1rem;
  cursor: pointer;
  box-shadow: 0 0 20px rgba(236, 72, 153, 0.7);
  animation: pulseGlow 1.5s infinite ease-in-out;
  margin-top: 2rem;
}

@keyframes pulseGlow {
  0% {
    box-shadow: 0 0 15px rgba(236, 72, 153, 0.4);
  }

  50% {
    box-shadow: 0 0 30px rgba(168, 85, 247, 0.9);
  }

  100% {
    box-shadow: 0 0 15px rgba(236, 72, 153, 0.4);
  }
}

/* Floating Stars */
.floating-stars {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  pointer-events: none;
  z-index: 0;
}

.star {
  position: absolute;
  font-size: 1.2rem;
  color: var(--pink);
  opacity: 0.5;
  animation: floatStar linear infinite;
  animation-duration: calc(8s + (random(var(--n)) * 4s));
  filter: drop-shadow(0 0 6px rgba(236, 72, 153, 0.5));
}

@keyframes floatStar {
  0% {
    transform: translateY(0);
    opacity: 0.3;
  }

  50% {
    opacity: 1;
  }

  100% {
    transform: translateY(-100vh);
    opacity: 0;
  }
}

/* AI Generator Styles */
.ai-generator {
  max-width: 600px;
  margin: 0 auto;
  padding: 2rem;
  background: linear-gradient(145deg, rgba(255, 255, 255, 0.9), rgba(252, 231, 243, 0.8));
  border-radius: 20px;
  box-shadow: 0 8px 25px rgba(236, 72, 153, 0.2);
  border: 2px solid var(--pink-light);
}

.ai-description {
  font-size: 1.2rem;
  color: var(--pink-darker);
  margin-bottom: 1.5rem;
  font-style: italic;
}

.input-group {
  display: flex;
  flex-direction: column;
  gap: 1rem;
  margin-bottom: 2rem;
}

.ai-input {
  padding: 1rem;
  font-size: 1.1rem;
  border: 2px solid var(--pink-mid);
  border-radius: 25px;
  text-align: center;
  background: linear-gradient(135deg, #ffffff, #fdf2f8);
  color: var(--pink-darker);
  outline: none;
  transition: all 0.3s ease;
}

.ai-input:focus {
  border-color: var(--purple);
  box-shadow: 0 0 15px rgba(168, 85, 247, 0.3);
}

.ai-output {
  background: linear-gradient(135deg, #fdf2f8, #ffffff);
  border: 2px solid var(--pink);
  border-radius: 15px;
  padding: 1.5rem;
  margin: 1.5rem 0;
  box-shadow: 0 4px 15px rgba(236, 72, 153, 0.1);
}

.typing-container h3 {
  color: var(--pink-dark);
  margin-bottom: 1rem;
  font-size: 1.4rem;
}

.poem-text {
  font-family: 'Caveat', cursive;
  font-size: 1.3rem;
  line-height: 1.6;
  color: var(--pink-darker);
  white-space: pre-line;
  text-align: left;
  padding: 1rem;
  background: rgba(252, 231, 243, 0.3);
  border-radius: 10px;
  border-left: 4px solid var(--pink);
}

.poem-text.typing::after {
  content: '|';
  animation: blink 1s infinite;
  color: var(--pink);
}

@keyframes blink {

  0%,
  50% {
    opacity: 1;
  }

  51%,
  100% {
    opacity: 0;
  }
}

.ai-suggestions {
  font-size: 0.9rem;
  color: #6b7280;
  font-style: italic;
  margin-top: 1rem;
}

.animated-btn:disabled {
  opacity: 0.7;
  cursor: not-allowed;
  transform: none;
}

.animated-btn:disabled:hover {
  transform: none;
  box-shadow: 0 4px 20px rgba(236, 72, 153, 0.4);
}

/* Music Control */
.music-control {
  position: fixed;
  top: 20px;
  right: 20px;
  z-index: 1000;
}

.music-btn {
  background: linear-gradient(135deg, var(--pink-mid), var(--purple));
  color: white;
  border: none;
  border-radius: 50%;
  width: 50px;
  height: 50px;
  font-size: 1.2rem;
  cursor: pointer;
  box-shadow: 0 4px 15px rgba(236, 72, 153, 0.4);
  transition: all 0.3s ease;
  display: flex;
  align-items: center;
  justify-content: center;
}

.music-btn:hover {
  transform: scale(1.1);
  box-shadow: 0 6px 20px rgba(168, 85, 247, 0.5);
}

.music-btn.playing {
  animation: musicPulse 2s infinite ease-in-out;
}

@keyframes musicPulse {

  0%,
  100% {
    box-shadow: 0 4px 15px rgba(236, 72, 153, 0.4);
    transform: scale(1);
  }

  50% {
    box-shadow: 0 6px 25px rgba(168, 85, 247, 0.7);
    transform: scale(1.05);
  }
}
</style>






