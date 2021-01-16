<template>
	<div>
		<div class="avatar">
			<img :src="value" />
		</div>
	</div>
</template>

<script>
export default {
	props: {
		value: {
			type: String,
			required: true,
		},
	}
}
</script>

<style scoped>
	.avatar {
		display: flex;
		justify-content: center;
	}
	.avatar img {
		width: 150px;
		height: auto;
		border-radius: 50%;
	}
</style>
