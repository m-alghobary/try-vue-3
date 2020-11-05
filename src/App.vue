<template>
	<img src="/img/title.png" class="mx-auto mt-5" />
	<div class="max-w-xl mt-5 mx-auto p-4 grid grid-cols-4 grid-rows-4 gap-6">
		<game-card
			v-for="(card, index) in cards"
			:key="index"
			:type="card.type"
			:matched="card.matched"
			:isFlipped="card.isFlipped"
			:pos="card.pos"
			@selected="selectCard"
		></game-card>
	</div>

	<p class="text-teal-400 text-center text-lg font-medium mt-4 bg-gray-700 py-3 bg-opacity-75">{{ unCoverdPairs }} Uncoverd pairs</p>
</template>

<script>
import shuffle from 'lodash/shuffle';
import { ref, watch, computed } from 'vue';
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
				isFlipped: true,
				matched: false,
				pos: null,
			});

			cards.value.push({
				type,
				isFlipped: true,
				matched: false,
				pos: null,
			});
		});

		cards.value = shuffle(cards.value);

		cards.value = cards.value.map((card, i) => ({
			...card,
			pos: i,
		}));

		const unCoverdPairs = ref(1);

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
				console.log('JI');
			}
		});

		return {
			cards,
			selectCard,
			unCoverdPairs,
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
