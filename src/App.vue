<template>
	<h1 class="text-6xl text-center font-extrabold mt-4 bg-clip-text text-transparent bg-gradient-to-r from-yellow-500 via-green-500 to-yellow-500">
		Peek A Card
	</h1>
	<div class="max-w-xl mx-auto mt-2 relative">
		<transition-group tag="section" class="p-4 grid grid-cols-4 grid-rows-4 gap-6" name="shuffle-card">
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

		<div v-if="isNewGame || !unCoverdPairs" class="absolute inset-0 w-full h-full bg-gray-900 rounded text-4xl text-white bg-opacity-75">
			<div v-if="!unCoverdPairs" class="flex flex-col h-full items-center justify-center">
				<span>You Wins!</span>
				<button
					class="bg-gradient-to-r from-green-400 to-teal-500 text-white text-lg font-medium mt-4 block py-4 px-8 rounded-full"
					@click="restartGame"
				>
					Replay
				</button>
			</div>
			<div v-else class="flex flex-col h-full items-center justify-center">
				<span>Welcom</span>
				<button
					class="bg-gradient-to-r from-green-400 to-teal-500 text-white text-lg font-medium mt-4 block py-4 px-8 rounded-full"
					@click="restartGame"
				>
					Play
				</button>
			</div>
		</div>
	</div>

	<p class="text-teal-400 text-center text-lg font-medium mt-4 bg-gray-700 py-3 bg-opacity-75">
		<span v-if="unCoverdPairs">{{ unCoverdPairs }} Uncoverd pairs</span>
		<span v-else>You Wins!</span>
	</p>
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
		const isNewGame = ref(true);
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
			isNewGame.value = false;

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
			isNewGame,
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
