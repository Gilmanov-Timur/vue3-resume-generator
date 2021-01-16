<template>
	<p
		contenteditable
		@input="$emit('updateContent', $event.target.textContent)"
	>{{ value }}</p>
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
