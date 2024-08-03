<template>
  <main class="bg-black h-screen w-screen relative overflow-hidden">
    <div v-if="!gameOver">
      <div class="absolute top-2 left-2 text-white">Score: {{ score }}</div>
      <div class="absolute top-2 right-2 text-white">
        Difficulty: {{ difficulty.toFixed(1) }}
      </div>
      <FallingWords :words="activeWords" />
      <Projectile
        v-for="projectile in projectiles"
        :key="projectile.id"
        :projectile="projectile"
      />
      <Ship :rotation="shipRotation" />
    </div>
    <div v-else class="flex flex-col items-center justify-center h-full">
      <h1 class="text-4xl text-white mb-4">Game Over</h1>
      <p class="text-2xl text-white mb-4">Final Score: {{ score }}</p>
      <UButton @click="restartGame" label="Restart Game" color="white" />
      <div
        class="fixed bottom-0 p-4 w-full bg-white flex items-center justify-between gap-4 text-gray-700"
      >
        <div class="flex items-center justify-between gap-2">
          <img
            src="https://supersaas.dev/logo.png"
            alt="SuperSaas Logo"
            class="h-6 w-auto"
          />
          <p>
            Made with ❤️ by
            <a href="https://supersaas.dev" class="underline" target="_blank"
              >SuperSaas</a
            >. The Nuxt 3 Fullstack Starter kit.
          </p>
        </div>
        <p>
          Game source code on
          <a
            href="https://github.com/fayazara/Framework-Wars"
            class="underline"
            target="_blank"
            >GitHub</a
          >
        </p>
      </div>
    </div>
  </main>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from "vue";
import FallingWords from "./components/FallingWords.vue";
import Projectile from "./components/Projectile.vue";
import Ship from "./components/Ship.vue";

const wordList = [
  "Next",
  "Angular",
  "Vue",
  "React",
  "Javascript",
  "Typescript",
  "Svelte",
  "Angular",
  "Node",
  "Rails",
  "Java",
  "PHP",
  "Ruby",
  "Laravel",
];

const score = ref(0);
const activeWords = ref([]);
const projectiles = ref([]);
const shipRotation = ref(0);
const difficulty = ref(1);
const maxActiveWords = ref(10);
const gameOver = ref(false);

const addWord = () => {
  if (activeWords.value.length < maxActiveWords.value) {
    const word = wordList[Math.floor(Math.random() * wordList.length)];
    activeWords.value.push({
      id: Date.now(),
      word,
      x: Math.random() * (window.innerWidth - 100),
      y: -50,
      speed: 0.5 + Math.random() * 0.5 * difficulty.value,
    });
  }
};

const updateWords = () => {
  activeWords.value.forEach((word) => {
    word.y += word.speed;
    if (word.y > window.innerHeight) {
      endGame();
    }
  });
};

const updateProjectiles = () => {
  projectiles.value.forEach((projectile) => {
    const dx = projectile.targetX - projectile.x;
    const dy = projectile.targetY - projectile.y;
    const distance = Math.sqrt(dx * dx + dy * dy);

    if (distance < 5) {
      projectiles.value = projectiles.value.filter(
        (p) => p.id !== projectile.id
      );
    } else {
      const speed = 10;
      const ratio = speed / distance;
      projectile.x += dx * ratio;
      projectile.y += dy * ratio;
    }
  });
};

const handleKeyPress = (event) => {
  if (gameOver.value) return;

  const letter = event.key.toLowerCase();
  const targetWord = activeWords.value.find((word) =>
    word.word.toLowerCase().startsWith(letter)
  );

  if (targetWord) {
    const angle = Math.atan2(
      targetWord.y - (window.innerHeight - 48),
      targetWord.x - window.innerWidth / 2
    );
    shipRotation.value = (angle * 180) / Math.PI + 90;

    projectiles.value.push({
      id: Date.now(),
      x: window.innerWidth / 2,
      y: window.innerHeight - 48,
      targetX: targetWord.x + 50,
      targetY: targetWord.y + 20,
      letter,
    });

    targetWord.word = targetWord.word.slice(1);
    if (targetWord.word.length === 0) {
      activeWords.value = activeWords.value.filter(
        (w) => w.id !== targetWord.id
      );
      score.value += 10;
      increaseDifficulty();
    } else {
      score.value++;
    }
  }
};

const increaseDifficulty = () => {
  difficulty.value += 0.1;
  maxActiveWords.value = Math.min(20, Math.floor(10 + difficulty.value * 2));
};

const endGame = () => {
  gameOver.value = true;
  clearAllIntervals();
};

const restartGame = () => {
  score.value = 0;
  activeWords.value = [];
  projectiles.value = [];
  shipRotation.value = 0;
  difficulty.value = 1;
  maxActiveWords.value = 10;
  gameOver.value = false;
  startGame();
};

const clearAllIntervals = () => {
  clearInterval(gameLoop);
  clearInterval(wordGenerationInterval);
  clearInterval(difficultyIncreaseInterval);
};

let gameLoop;
let wordGenerationInterval;
let difficultyIncreaseInterval;

const startGame = () => {
  gameLoop = setInterval(() => {
    updateWords();
    updateProjectiles();
  }, 16);

  wordGenerationInterval = setInterval(() => {
    addWord();
    clearInterval(wordGenerationInterval);
    wordGenerationInterval = setInterval(addWord, 2000 / difficulty.value);
  }, 2000 / difficulty.value);

  difficultyIncreaseInterval = setInterval(() => {
    increaseDifficulty();
  }, 1000);
};

onMounted(() => {
  window.addEventListener("keypress", handleKeyPress);
  startGame();
});

onUnmounted(() => {
  window.removeEventListener("keypress", handleKeyPress);
  clearAllIntervals();
});
</script>
