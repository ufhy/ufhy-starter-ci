<template>
	<v-list-tile>
		<v-list-tile-content>
			<v-list-tile-title>{{title}}</v-list-tile-title>
			<v-list-tile-sub-title>{{description}}</v-list-tile-sub-title>
		</v-list-tile-content>
		<v-list-tile-action>
			<template v-if="editable">
				<v-edit-dialog lazy large
					:return-value="value"
					@save="editDialogSave"
					@open="editDialogOpen">
					<template v-if="inputType === 'text'">
						<div class="text-xs-right font-weight-bold">{{value}}</div>
						<v-text-field single-line counter
							slot="input"
							v-model="inputValue"
							:rules="[max255chars]"
							label="Edit"
						></v-text-field>
					</template>
					<template v-if="inputType === 'select'">
						<div class="text-xs-right font-weight-bold">{{selectValue}}</div>
						<v-select single-line
							slot="input"
							v-model="inputValue"
							label="Edit"
							:items="optionItems"
						></v-select>
					</template>
				</v-edit-dialog>
			</template>
			<template v-else>
				<div class="text-xs-right font-weight-bold">{{value}}</div>
			</template>
		</v-list-tile-action>
	</v-list-tile>
</template>

<script>
export default {
	name: 'setting-row',
	props: {
		editable: {
			type: Boolean,
			default: false,
		},
		title: String,
		description: String,
		value: String,
		inputType: {
			type: String,
			required: true
		},
		inputOptions: String,
	},
	data() {
		return {
			max255chars: v => v.length <= 255 || 'Input too long!',
			inputValue: '',
		}
	},
	computed: {
		optionItems() {
			if (this.inputOptions) {
				const inputOptions = this.inputOptions.split('|');
				let options = [];
				for (const option in inputOptions) {
					if (inputOptions.hasOwnProperty(option)) {
						const element = inputOptions[option].split('=');
						options.push({
							value: element[0],
							text: element[1]
						});
					}
				}

				return options;
			}

			return [];
		},
		selectValue() {
			const findIndex = this.$lodash.findIndex(this.optionItems, {value: this.value});
			if (findIndex > -1) {
				return this.optionItems[findIndex].text;
			}
			return ''
		}
	},
	methods: {
		editDialogOpen() {
			this.inputValue = this.value;
		},
		editDialogSave() {
			if (this.inputValue !== this.value) {
				this.$emit('save', this.inputValue);
			}
		}
	}
}
</script>
