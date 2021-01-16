<template>
	<h1
		contenteditable
		@input="$emit('updateContent', $event.target.textContent)"
	>{{ value }}</h1>
</template>

<script>
export default {
	props: {
		value: {
			type: String,
			required: true,
		},
		emits: {
			updateContent(str) {
				if (
					typeof str === 'string'
				) {
					return true
				} else {
					console.warn('Invalid updateContent emit param!')
					return false
				}
			}
		}
	},
}
</script>
