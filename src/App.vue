<template>
	<h1 class="text-center mt-6 text-2xl">Peek-A-Vue</h1>
	<div class="max-w-2xl mt-5 mx-auto p-4 grid grid-cols-4 grid-rows-4 gap-4">
		<game-card v-for="(card, index) in cards" :key="index" :type="card"></game-card>
	</div>
</template>

<script>
import { ref } from 'vue';
import GameCard from './components/game-card.vue';

export default {
	name: 'App',
	components: {
		GameCard,
	},
	setup() {
		const cards = ref([]);

		for (let i = 0; i < 16; i++) {
			cards.value.push(i);
		}

		return {
			cards,
		};
	},
};
</script>
