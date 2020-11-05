<template>
	<div :class="{ 'cursor-pointer': !matched }" class="w-32 h-32 relative rounded card" @click="flip">
		<div class="content absolute inset-0 w-full h-full rounded" :class="{ flip: !visible }">
			<div
				:class="{ 'border-4 border-green-500': matched }"
				class="face bg-teal-200 absolute inset-0 rounded shadow-md flex items-center justify-center"
			>
				<span class="text-5xl font-bold text-pink-500">{{ type }}</span>
			</div>
			<div class="back bg-teal-400 absolute inset-0 rounded shadow-md"></div>
		</div>
	</div>
</template>

<script>
import { ref, watch } from 'vue';

export default {
	props: {
		type: {
			type: Number,
			required: true,
		},
		isFlipped: {
			type: Boolean,
			default: true,
		},
		matched: {
			type: Boolean,
			default: false,
		},
		pos: {
			type: Number,
			required: true,
		},
	},
	setup(props, { emit }) {
		const visible = ref(true);

		watch(
			() => props.isFlipped,
			(val) => {
				visible.value = val;
			},
			{ immediate: true }
		);

		function flip() {
			if (props.matched) return;

			visible.value = !visible.value;
			emit('selected', {
				type: props.type,
				isFlipped: visible.value,
				pos: props.pos,
			});
		}

		return {
			visible,
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
