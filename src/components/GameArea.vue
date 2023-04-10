<template>
	<div v-if="gameStarted" class="gameArea">
    <div class="gameArea__timer">{{ timeRemaining }}</div>
    <div class="gameArea__score">分數: {{ score }}</div>
    <div class="gameArea__target">{{ currentTarget }}</div>
    <input ref="gameInput" type="text" @keyup="onKeyUp" @keydown="onKeyDown"/>
		<span class="gameArea__tips" v-if="gameMode === 'word'">輸入完成後請按下 Enter</span>
  </div>
</template>

<script setup>
	import { defineProps, defineEmits, ref } from "vue";

	const props = defineProps({
		gameStarted: {
			type: Boolean,
			default: false,
		},
		timeRemaining: {
			type: Number,
			default: 60,
		},
		score: {
			type: Number,
			default: 0,
		},
		currentTarget: {
			type: String,
			default: "",
		},
		gameMode: {
			type: String,
		}
	});

	const emit = defineEmits(["keyUp", "keyDown"]);

	const gameInput = ref(null);

	const onKeyUp = (event) => {
		emit("keyUp", event);
	}

	const onKeyDown = (event) => {
		emit("keyDown", event);
	};
</script>

<style lang="scss" scoped>
	.gameArea {
		display: flex;
		flex-direction: column;
		align-items: center;
		color: white;
		gap: 1rem;
		&__timer {
			font-size: 6rem;
			font-weight: bold;
		}

		&__score {
			font-size: 2rem;
		}

		&__target {
			font-size: 3rem;
			font-weight: bold;
			height: 72px;
		}

		&__tips {
			display: inline-block;
			font-size: .6rem;
		}

		input {
			font-size: 1rem;
			padding: 0.5rem;
			width: 100%;
			max-width: 400px;
			border: 1px solid #333;
			border-radius: 4px;
			background-color: white;
			color: #333;
		}
	}
</style>
