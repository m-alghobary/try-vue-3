<template>
	<img src="/img/title.png" class="mx-auto mt-5" />
	<transition-group tag="section" class="max-w-xl mt-5 mx-auto p-4 grid grid-cols-4 grid-rows-4 gap-6" name="shuffle-card">
		<game-card
			v-for="card in cards"
			:key="`card-${card.type}-${card.order}`"
			:type="card.type"
			:matched="card.matched"
			:isFlipped="card.isFlipped"
			:pos="card.pos"
			@selected="selectCard"
		></game-card>
	</transition-group>

	<p class="text-teal-400 text-center text-lg font-medium mt-4 bg-gray-700 py-3 bg-opacity-75">
		<span v-if="unCoverdPairs">{{ unCoverdPairs }} Uncoverd pairs</span>
		<span v-else>You Wins!</span>
	</p>
	<div class="mt-5 text-center">
		<button class="bg-green-500 text-white py-2 px-4 rounded" @click="restartGame">
			<span v-if="!unCoverdPairs"> Restart Game </span>
			<span v-else> Start Game </span>
		</button>
	</div>
</template>

<script>
import { ref, watch, computed } from 'vue';
import shuffle from 'lodash/shuffle';
import { launchConfetti } from './utils/confetti.js';
import GameCard from './components/game-card.vue';

export default {
	name: 'App',
	components: {
		GameCard,
	},
	setup() {
		const cards = ref([]);
		const selectedCards = ref([]);
		const types = [1, 2, 3, 4, 5, 6, 7, 8];

		types.forEach((type, i) => {
			cards.value.push({
				type,
				isFlipped: false,
				matched: false,
				pos: null,
				order: 1,
			});

			cards.value.push({
				type,
				isFlipped: true,
				matched: false,
				pos: null,
				order: 2,
			});
		});

		cards.value = cards.value.map((card, i) => ({
			...card,
			pos: i,
		}));

		const unCoverdPairs = ref(8);

		function restartGame() {
			cards.value = shuffle(cards.value);
			console.log(cards.value);

			cards.value = cards.value.map((card, index) => {
				return {
					...card,
					matched: false,
					pos: index,
					isFlipped: true,
				};
			});

			unCoverdPairs.value = 8;
		}

		function selectCard(card) {
			if (selectedCards.value.length < 2) {
				selectedCards.value.push(card);
				cards.value[card.pos].isFlipped = card.isFlipped;
			}
		}

		function isMatch() {
			return selectedCards.value[0].type === selectedCards.value[1].type;
		}

		function flipBakc() {
			const firstCard = selectedCards.value[0];
			const secondCard = selectedCards.value[1];

			setTimeout(() => {
				cards.value[firstCard.pos].isFlipped = true;
				cards.value[secondCard.pos].isFlipped = true;
			}, 600);
		}

		watch(
			selectedCards,
			(value) => {
				if (value.length === 2) {
					if (!isMatch()) {
						flipBakc();
					} else {
						cards.value[selectedCards.value[0].pos].matched = true;
						cards.value[selectedCards.value[1].pos].matched = true;
						unCoverdPairs.value--;
					}

					selectedCards.value.length = 0;
				}
			},
			{ deep: true }
		);

		watch(unCoverdPairs, (val) => {
			if (val === 0) {
				launchConfetti();
			}
		});

		return {
			cards,
			selectCard,
			unCoverdPairs,
			restartGame,
		};
	},
};
</script>

<style>
body {
	background-image: url('/img/page-bg.png');
	background-color: #333;
}

.shuffle-card-move {
	transition: transform 0.8s ease-in;
}
</style>
