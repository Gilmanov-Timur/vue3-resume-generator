<template>
	<div class="container list-item">
		<button
			class="btn warning"
			:class="{loading: resumeLoading}"
			@click="loadResume"
		>Загрузить резюме</button>
		<button
			class="btn warning"
			:class="{loading: resumeSaving}"
			@click="saveResume"
			:disabled="!this.blocks.length"
		>Сохранить резюме</button>
	</div>
	<div class="container column">
		<AppForm @addBlock="addBlock" />
		<div class="card card-w70">
			<div
				class="component"
				v-for="(block, index) in blocks"
				v-if="blocks.length"
				:key="block.id"
			>
				<div class="action-buttons">
					<button @click="moveUp(index)" :disabled="index === 0">&uarr;</button>
					<button @click="moveDown(index)" :disabled="index === blocks.length - 1">&darr;</button>
					<button @click="removeBlock(index)">x</button>
				</div>
				<component
					:is="block.component"
					:value="block.value"
					@updateContent="updateBlockContent($event, index)"
				/>
			</div>
			<h3 v-else>Добавьте первый блок, чтобы увидеть результат</h3>
		</div>
	</div>
	<AppComments v-bind="comments" @loadComments="loadComments" />
</template>

<script>
export default {
	data() {
		return {
			resumeLoading: false,
			resumeSaving: false,
			blocks: [],
			comments: {
				loading: false,
				list: [],
			},
		}
	},
	methods: {
		sendRequest(url, method = 'GET', body = null) {
			return fetch(url, {
				method: method,
				body: body,
				headers: {
					'Content-Type': 'application/json'
				}
			}).then(response => {
				if (response.ok) {
					return response.json()
				}
				throw Error('Ошибка: ' + response.status)
			})
		},
		addBlock({type, value}) {
			this.blocks.push({
				id: Date.now(),
				component: 'app-' + type,
				value
			})
		},
		async loadComments() {
			this.comments.loading = true

			try {
				const data = await this.sendRequest('https://jsonplaceholder.typicode.com/comments?_limit=42')

				this.comments.list = data.map(comment => ({
					id: comment.id,
					email: comment.email,
					body: comment.body,
				}))
			} catch (e) {
				console.warn(e)
			} finally {
				this.comments.loading = false
			}
		},
		async loadResume() {
			if (this.resumeLoading) {
				return
			}

			this.resumeLoading = true

			try {
				const data = await this.sendRequest('https://vue3-fdbb3-default-rtdb.firebaseio.com/resume.json')

				if (data) {
					this.blocks = data
				} else {
					alert('Резюме не найдено')
				}
			} catch (e) {
				console.warn(e)
			} finally {
				this.resumeLoading = false
			}
		},
		async saveResume() {
			if (this.resumeSaving) {
				return
			}

			//const validBlocks = this.blocks.filter(block => block.value)
			console.log(this.validBlocks)

			if (!this.validBlocks.length) {
				alert('Нечего сохранять')
				return
			}

			this.resumeSaving = true

			try {
				await this.sendRequest(
					'https://vue3-fdbb3-default-rtdb.firebaseio.com/resume.json',
					'PUT',
					JSON.stringify(this.validBlocks)
				)
				alert('Резюме успешно сохранено')
			} catch (e) {
				console.warn(e)
			} finally {
				this.resumeSaving = false
			}
		},
		moveUp(index) {
			if (index !== 0) {
				this.blocks.splice(index - 1, 0, this.blocks.splice(index, 1)[0])
			}
		},
		moveDown(index) {
			if (index !== this.blocks.length - 1) {
				this.blocks.splice(index + 1, 0, this.blocks.splice(index, 1)[0]);
			}
		},
		removeBlock(index) {
			this.blocks.splice(index, 1);
		},
		updateBlockContent(text, index) {
			this.blocks[index].value = text
		},
	},
	computed: {
		validBlocks() {
			return this.blocks.filter(block => block.value)
		}
	},
	components: {
		AppForm: require('@/AppForm').default,
		AppTitle: require('@/AppTitle').default,
		AppSubtitle: require('@/AppSubtitle').default,
		AppAvatar: require('@/AppAvatar').default,
		AppText: require('@/AppText').default,
		AppComments: require('@/AppComments').default,
	},
}
</script>
