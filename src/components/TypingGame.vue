<template>
  <div class="typingGameWrap">
    <div class="typingGameContainer">
      <h2 v-if="!gameStarted">Typing Game</h2>
      <div v-if="countdown > 0" class="typingGameContainer__countdown">{{ countdown }}</div>
      <GameArea
        v-else
        :gameStarted="gameStarted"
        :timeRemaining="timeRemaining"
        :score="score"
        :currentTarget="currentTarget"
        :gameMode="gameMode"
        @keyDown="onKeyDown"
        @keyUp="onKeyUp"
      />
      <ControlPanel
        :gameStarted="gameStarted"
        :countdown="countdown"
        @startGame="startGame"
        @pauseResumeGame="pauseResumeGame"
        @stopGame="stopGame"
      />
    </div>
  </div>
</template>
<script setup>
import { ref, computed, onMounted } from "vue";
import axios from "axios";
import GameArea from './GameArea.vue';
import ControlPanel from './ControlPanel.vue';

const wordList = ref([]);
const gameStarted = ref(false);
const gamePaused = ref(false);
const gameMode = ref("");
const timeRemaining = ref(60);
const score = ref(0);
const currentTarget = ref("");
const typedCharacters = ref(0);
const correctCharacters = ref(0);
const countdown = ref(0);

const startGame = async (mode) => {
  gameStarted.value = true;
  gameMode.value = mode;

  // 初始化遊戲數據
  timeRemaining.value = 60;
  score.value = 0;
  typedCharacters.value = 0;
  correctCharacters.value = 0;
  gamePaused.value = false;

  countdown.value = 3;

  while (countdown.value > 0) {
    await new Promise((resolve) => setTimeout(resolve, 1000));
    countdown.value -= 1;
  }

  // 正式開始遊戲
  setNewTarget();
  gameTimer();
};

const pauseResumeGame = () => {
  gamePaused.value = !gamePaused.value;
  if (!gamePaused.value) {
    gameTimer();
  }
};

const stopGame = () => {
  gameStarted.value = false;
  timeRemaining.value = 0;
  showResults();
};

const gameTimer = () => {
  if (timeRemaining.value > 0 && !gamePaused.value) {
    setTimeout(() => {
      timeRemaining.value--;
      gameTimer();
    }, 1000);
  } else if (timeRemaining.value === 0) {
    gameStarted.value = false;
    showResults();
  }
};

const setNewTarget = () => {
  if (gameMode.value === "character") {
    currentTarget.value = getRandomCharacter();
  } else if (gameMode.value === "word") {
    currentTarget.value = getRandomWord();
  }
};

const getRandomCharacter = () => {
  const chars = "abcdefghijklmnopqrstuvwxyz0123456789";
  return chars[Math.floor(Math.random() * chars.length)];
};

const getRandomWord = () => {
  return wordList.value[Math.floor(Math.random() * wordList.value.length)];
};

const onKeyDown = (event) => {
  if (event.code === 'Space') {
    event.preventDefault();
    pauseResumeGame();
  }

  if (event.code === 'Escape') {
    stopGame();
  }
}

const onKeyUp = (event) => {
  const input = event.target.value;
  if (gameMode.value === "character") {
    // 針對單字模式做處理
    typedCharacters.value += 1;

    if (input === currentTarget.value) {
      score.value += 100;
      correctCharacters.value += 1;
      setNewTarget();
      event.target.value = "";
    }
  } else if (gameMode.value === "word") {
    // 針對單詞文字做處理
    if (event.code === "Enter") {
      typedCharacters.value += input.length;

      if (input === currentTarget.value) {
        score.value += 100 * currentTarget.value.length;
        correctCharacters.value += currentTarget.value.length;
        setNewTarget();
      } else {
        if ( score.value > 0 ) {
          score.value -= 25
        }
      }
      event.target.value = "";
    }
  }
};

const showResults = () => {
  const accuracy = typedCharacters.value ? (correctCharacters.value / typedCharacters.value) * 100 : 0
  
  alert(`遊戲結束！\n分數：${score.value}\n準確率：${accuracy.toFixed(2)}%`);
};

const fetchWordList = async () => {
  const response = await axios.get(
    "https://raw.githubusercontent.com/bitcoin/bips/master/bip-0039/english.txt"
  );
  wordList.value = response.data.split("\n");
};

onMounted(() => {
  fetchWordList();
})

</script>
<style scoped lang="scss">
.typingGameWrap {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 100vw;
  height: 100vh;
  background-image: url('../assets/background.png');
  background-position: center;
  background-size: cover;
  background-repeat: no-repeat;
}
.typingGameContainer {
  position: relative;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
  width: 800px;
  min-height: 700px;
  margin: 0 auto;
  background: rgba(0, 0, 0, .6);
  h2 {
    font-size: 3rem;
    font-weight: bold;
    color: white;
    margin-top: auto;
  }
  &__countdown {
    position: absolute;
    top: 30%;
    font-size: 6rem;
    color: white;
    font-weight: bold;
    text-align: center;
    transform-origin: center;
    animation-name: CountDown;
    animation-duration: 1s;
    animation-iteration-count: infinite;
  }
}

@keyframes CountDown {
  from {
    transform: scale(.2);
    opacity: 1;
  }
  to {
    transform: scale(1);
    opacity: 0;
  }
}
</style>