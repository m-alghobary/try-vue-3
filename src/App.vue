<template>
	<img src="/img/title.png" class="mx-auto mt-5" />
	<div class="max-w-2xl mt-5 mx-auto p-4 grid grid-cols-4 grid-rows-4 gap-4">
		<game-card v-for="(card, index) in cards" :key="index" :type="card" @selected="selectCard"></game-card>
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
		const selectedCards = ref([]);

		for (let i = 0; i < 16; i++) {
			cards.value.push(i);
		}

		function selectCard(card) {
			if (selectedCards.value.length < 2) selectedCards.value.push(card);
			console.log(selectedCards.value);
		}

		return {
			cards,
			selectCard,
		};
	},
};
</script>

<style>
body {
	background-image: url('/img/page-bg.png');
	background-color: #333;
}
</style>
