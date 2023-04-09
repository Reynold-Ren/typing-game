<template>
  <div class="typingGameWrap">
    <div class="typingGameContainer">
      <GameArea />
      <ControlPanel />
    </div>
  </div>
</template>
<script setup>
import { ref, computed, onMounted } from "vue";
import axios from "axios";
import GameArea from './GameArea.vue';
import ControlPanel from './ControlPanel.vue';

const wordList = ref([]);

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
  width: 800px;
  min-height: 700px;
  margin: 0 auto;
  background: rgba(0, 0, 0, .6);
}
</style>