<template>
	<div class="w-32 h-32 relative rounded cursor-pointer card" @click="flip">
		<div class="content absolute inset-0 w-full h-full" :class="{ flip: !isFlipped }">
			<div
				class="face bg-teal-300 absolute inset-0 w-full h-full rounded shadow-md flex items-center justify-center text-3xl font-semibold text-pink-500"
			>
				{{ type }}
			</div>
			<div class="back bg-teal-400 absolute inset-0 w-full h-full rounded shadow-md"></div>
		</div>
	</div>
</template>

<script>
import { ref } from 'vue';

export default {
	props: {
		type: {
			type: Number,
			required: true,
		},
	},
	setup(props) {
		const isFlipped = ref(true);

		function flip() {
			isFlipped.value = !isFlipped.value;
		}

		return {
			isFlipped,
			flip,
		};
	},
};
</script>

<style scoped>
.card {
	perspective: 500px;
}

.content {
	transition: transform 1s;
	transform-style: preserve-3d;
}

.flip {
	transform: rotateY(180deg);
	transition: transform 0.5s;
}

.face,
.back {
	backface-visibility: hidden;
}

.face {
	transform: rotateY(180deg);
}
</style>
