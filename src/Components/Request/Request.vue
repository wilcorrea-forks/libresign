<template>
	<div class="form-rs-container">
		<div v-if="hasUsers" class="list-users-selected">
			<div id="title">
				<span>{{ t('libresign', 'Users') }}</span>
			</div>
			<ul class="list-users">
				<li v-for="value in values" :key="value.email" class="list-uses-item">
					<ListItem :user="value" :description="value.description" @remove-user="removeValue" />
				</li>
			</ul>

			<button class="primary btn" @click.prevent="send">
				{{ t('libresign', 'Submit Request') }}
			</button>
		</div>
		<slot name="actions" />

		<template v-if="showModal">
			<div class="oc-dialog"
				tabindex="-1"
				role="dialog"
				style="display: inline-block; position: fixed; width: 600px; height: 500px;">
				<h2 class="oc-dialog-title">
					{{ t('libresign', 'Add User on Request') }}
				</h2>
				<a class="oc-dialog-close" @click="showModal = false" />
				<div id="oc-dialog-filepicker-content" class="oc-dialog-content" style="height: calc(100% - 102px);">
					<form @submit="e => e.preventDefault()">
						<div>
							<input v-model="email" type="email" :placeholder="t('libresign', 'Email')">
							<small>{{ t('libresign', '* The email is required') }}</small>
						</div>
						<div>
							<input v-model="description" type="text" :placeholder=" t('libresign', 'Description')">
						</div>
					</form>
				</div>
				<div class="oc-dialog-buttonrow onebutton aside">
					<button class="btn-inc" @click="showModal = false">
						{{ t('libresign', 'Cancel') }}
					</button>
					<button :disabled="!hasEmail" class="primary btn-inc" @click="addUser">
						{{ t('libresign', 'Add') }}
					</button>
				</div>
			</div>
		</template>

		<div class="form-rs-container__buttons">
			<button class="btn-inc" @click="$emit('cancel')">
				{{ t('libresign', 'Cancel') }}
			</button>
			<button v-if="!showModal" class="btn-inc primary" @click="showModal = true">
				{{ t('libresign', 'Add User') }}
			</button>
		</div>
	</div>
</template>

<script>
import ListItem from '../ListItem'
import { validateEmail } from '../../utils/validators'
export default {
	name: 'Request',
	components: {
		ListItem,
	},
	props: {
		fileinfo: {
			type: Object,
			default: () => {},
			required: true,
		},
		items: {
			type: Array,
			default: () => [],
			required: false,
		},
	},
	data() {
		return {
			values: [],
			email: '',
			description: '',
			showModal: false,
		}
	},
	computed: {
		hasUsers(val) {
			return !(this.values.length <= 0)
		},
		hasEmail(val) {
			return validateEmail(this.email)
		},
	},
	watch: {
		items(newVal) {
			this.values = newVal
		},
	},
	methods: {
		removeValue(value) {
			this.values = this.values.filter(ft => {
				return ft !== value
			})
		},
		emitDelete(value) {
			this.$emit('request:delete', value)
		},
		addUser() {
			this.values.push({
				email: this.email,
				description: this.description,
			})
			this.clearForm()
		},
		clearForm() {
			this.email = ''
			this.description = ''
			this.showModal = false
		},
		async send(param) {
			this.$emit('request:signatures', this.values)
		},
		clearList() {
			this.values = []
		},
	},
}
</script>
<style lang="scss" scoped>
@import './styles';
</style>
