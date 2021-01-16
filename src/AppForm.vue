<template>
	<form class="card card-w30" @submit.prevent="onSubmit">
		<div class="form-control">
			<label for="type">Тип блока</label>
			<select id="type" v-model="blockType">
				<option value="title">Заголовок</option>
				<option value="subtitle">Подзаголовок</option>
				<option value="avatar">Аватар</option>
				<option value="text">Текст</option>
			</select>
		</div>

		<div class="form-control">
			<label for="value">Значение</label>
			<textarea id="value" rows="3" v-model.trim="blockValue"></textarea>
		</div>

		<button class="btn primary" :disabled="!formIsValid">Добавить</button>
	</form>
</template>

<script>
export default {
	emits: {
		addBlock(props) {
			if (
				typeof props === 'object'
				&& typeof props.type === 'string'
				&& typeof props.value === 'string'
			) {
				return true
			} else {
				console.warn('Invalid addBlock emit params!')
				return false
			}
		}
	},
	data() {
		return {
			blockType: 'title',
			blockValue: '',
		}
	},
	methods: {
		onSubmit() {
			this.$emit('addBlock', {
				type: this.blockType,
				value: this.blockValue
			})
			this.blockType = 'title'
			this.blockValue = ''
		}
	},
	computed: {
		formIsValid() {
			return this.blockValue.length > 3
		}
	}
}
</script>
