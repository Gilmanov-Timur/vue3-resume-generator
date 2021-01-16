<template>
	<div class="container">
		<div class="card" v-if="list.length">
			<h2>Комментарии</h2>
			<ul class="list">
				<li class="list-item" v-for="comment in list" :key="comment.id">
					<div>
						<p><strong>{{ comment.email }}</strong></p>
						<small>{{ comment.body }}</small>
					</div>
				</li>
			</ul>
		</div>
		<p v-else-if="!loading">
			<button class="btn primary" @click="$emit('loadComments')">Загрузить комментарии</button>
		</p>
		<div class="loader" v-else></div>
	</div>
</template>

<script>
export default {
	props: {
		loading: {
			type: Boolean,
			required: false,
			default: false,
		},
		list: {
			type: Array,
			required: true,
			validator(comments) {
				return comments.every(comment => {
					return typeof comment === 'object'
					&& Object.keys(comment).length === 3
					&& typeof comment.id === 'number'
					&& typeof comment.email === 'string'
					&& typeof comment.body === 'string'
				})
			}
		},
	},
	emits: {
		loadComments: null
	},
}
</script>
