<template>
	<div class="bg-gray-300 w-32 h-32">{{ type }}</div>
</template>

<script>
export default {
	props: {
		type: {
			type: Number,
			required: true,
		},
	},
	setup(props) {
		return {};
	},
};
</script>

<style lang="scss" scoped></style>
