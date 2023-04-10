<template>
	<div :class="['controls', {selectMode: !gameStarted}]">
    <div v-if="!gameStarted">
			<button @click="emit('startGame', 'character')">字符模式</button>
    	<button @click="emit('startGame', 'word')">單詞模式</button>
		</div>
		<div v-if="gameStarted && countdown < 1">
			 <button @click="emit('pauseResumeGame')">
				暫停/恢復 (Space)
			</button>
			<button @click="emit('stopGame')">停止(Esc)</button>
		</div>
  </div>
</template>

<script setup>
	import { defineProps, defineEmits } from "vue";

	const props = defineProps({
		gameStarted: {
			type: Boolean,
			default: false,
		},
		countdown: {
			type: Number,
			default: 0
		}
	});

	const emit = defineEmits(['startGame'], ['pauseResumeGame', 'stopGame'])

	const onKeyDown = (event) => {
		if (event.code === 'Space') {
			event.preventDefault(); // 防止滾動頁面
			emit('pauseResumeGame');
		} else if (event.code === 'Escape') {
			emit('stopGame');
		}
	};
</script>

<style lang="scss" scoped>
.controls {
  display: flex;
  justify-content: center;
  margin: auto 0 1rem;
	&.selectMode {
		margin:  0 0 auto 0;
	}
  button {
    padding: 0.5rem 1rem;
    font-size: 1rem;
    background-color: #5c6bc0;
    border: none;
    color: #fff;
    cursor: pointer;
    border-radius: 4px;
    transition: background-color 0.3s;
    &:hover {
      background-color: #3f51b5;
    }
		&:first-child {
			margin-right: 1rem;
		}
  }
}
</style>