<template>
	<div class="w-32 h-32 relative rounded cursor-pointer" @click="flip">
		<div
			v-if="!isFlipped"
			class="bg-teal-300 absolute inset-0 w-full h-full rounded shadow-md flex items-center justify-center text-3xl font-semibold text-pink-500"
		>
			{{ type }}
		</div>
		<div v-else class="bg-teal-400 absolute inset-0 w-full h-full rounded shadow-md"></div>
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

<style lang="scss" scoped></style>
