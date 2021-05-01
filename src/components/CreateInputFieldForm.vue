<template>
	<b-card bg-variant="light" header-class="sticky-top card-header-bg" class="shadow">
		<template #header>
			<b-row align-h="between">
				<b-col>
					<header class="card-top-header">Input Field Details</header>
					<h3 class="mt-1 card-top-sub-header">Create input fields to populate the Form Below</h3>
				</b-col>
				<b-col>
					<b-button
						pill
						variant="success"
						size="sm"
						:disabled="inputValidityCheck"
						@click="addInput"
						style="position: absolute; right: 1.25rem;">Create Input Field</b-button>
				</b-col>
			</b-row>
		</template>

		<b-row class="m-0 pb-4">
			<b-col sm="6" md="4" class="mt-2 mb-2">
				<label for="label-title ">Enter Label for Input</label>
				<b-form-input
					id="label-title"
					v-model.trim="labelTitle"
					:required="true"
					:state="labelTitle.trim().length ? true : false"
					type="text"></b-form-input>
			</b-col>
			<b-col sm="6" md="4" class="mt-2 mb-2">
				<label for="input-type-selection">Select an Input Type</label>
				<b-form-select
					id="input-type-selection"
					v-model="selected"
					:options="options"></b-form-select>
			</b-col>
			<b-col sm="6" md="4" class="mt-2 mb-2">
				<label for="description-title ">Enter Additional Description</label>
				<b-form-input
					id="description-title"
					placeholder="Description"
					v-model.trim="additionalDescription"
					type="text"></b-form-input>
			</b-col>
			<b-col sm="6" md="4" class="mt-2 mb-2" v-if="placeholderInputTypes.includes(selected)">
				<label for="placeholder-title ">Enter Placeholder for Input Type</label>
				<b-form-input
					id="placeholder-title"
					v-model.trim="placeholderTitle"
					type="text"></b-form-input>
			</b-col>

			<template v-if="optionInputTypes.includes(selected)">
				<b-col sm="12" class="mt-2 mb-2">
					<template v-for="(inputInnerOption, index) in inputInnerOptions">
						<b-row :key="index" class="mt-2 mb-3">
							<b-col sm="1" style="margin: auto;text-align:left;">
								<template v-if="selected == 'checkbox'">
									<BIconCheckSquare />
								</template>
								<template v-else-if="selected == 'radio'">
									<BIconCircle />
								</template>
								<template v-else>
									{{index + 1}}.
								</template>
							</b-col>
							<b-col sm="6" class="pl-0">
								<b-form-input
									v-model.trim="inputInnerOptions[index]"
									placeholder="Enter Option"
									type="text"></b-form-input>
							</b-col>
							<b-col sm="1" style="margin: auto;cursor: pointer;" v-if="inputInnerOptions.length > 1">
								<BIconTrash
									@click="removeOption(index)"
									style="color:red;"
									v-b-popover.hover.top="'Delete Option'"/>
							</b-col>
							<b-col :sm="inputInnerOptions.length > 1 ? 4: 5" class="pl-0">
								<b-button
									pill
									variant="success"
									size="sm"
									style="padding: .25rem .35rem;"
									v-b-popover.hover.right="'Add New Option'"
									@click="addInnerOption"
									v-if="index + 1 == inputInnerOptions.length">
									<BIconPlus style="font-size:1.4rem;" />
									</b-button>
							</b-col>
						</b-row>
					</template>
				</b-col>
			</template>
		</b-row>
		<b-row class="m-0 pt-4" style="border-top:1px solid #ccc;" v-if="selected != 'toggle'">
			<b-col class="mt-2 mb-2">
				<b-row class="m-0">
					<label>Required</label>
					<b-col class="ml-4 p-0">
						<ToggleSwitch :default-state="isRequired" @change="updateRequiredStatus"/>
					</b-col>
				</b-row>
			</b-col>
		</b-row>
	</b-card>
</template>
<script>
import { BIconCircle, BIconCheckSquare, BIconTrash, BIconPlus } from 'bootstrap-vue';
import ToggleSwitch from './ToggleSwitch.vue';

export default {
  name: 'CreateInputFieldForm',
	components: {
		BIconCircle,
    BIconCheckSquare,
    BIconTrash,
    BIconPlus,
    ToggleSwitch
	},
	data() {
		return {
			options: [
        {text: "Text", value: 'text'},
        {text: "Text Area", value: 'textarea'},
        {text: "CheckBox",value: 'checkbox'},
        {text: "Radio Button", value: 'radio'},
        {text: "Select Dropdown", value: 'select' },
        {text: "Toggle Switch", value: 'toggle'},
        {text: "Date", value: 'date'},
        {text: "Time", value: 'time'},
      ],
      placeholderInputTypes: ['text', 'textarea'],
      optionInputTypes: ['checkbox', 'radio', 'select'],
      inputInnerOptions: ['Option'],
      labelTitle: '',
      placeholderTitle: '',
			additionalDescription: '',
      selected: 'text',
      isRequired: false
		};
	},
	computed: {
    inputValidityCheck() {
      return !this.labelTitle ||
        (this.optionInputTypes.includes(this.selected) &&
          (!this.inputInnerOptions.length ||
            this.inputInnerOptions.some(opt => !!opt.trim() ==  false)));
    },
  },
  watch: {
    selected: function(newValue, oldValue) {
      if(this.optionInputTypes.includes(oldValue) && !this.optionInputTypes.includes(newValue)) {
        this.inputInnerOptions = ['Option'];
      }
      if(this.placeholderInputTypes.includes(oldValue) && !this.placeholderInputTypes.includes(newValue)) {
        this.placeholderTitle = '';
      }
    }
  },
	methods: {
		addInput() {
      if(this.optionInputTypes.includes(this.selected)) {
        this.inputInnerOptions = this.inputInnerOptions.filter(opt => !!opt.trim())
      }

			const newInputField = {
				label: this.labelTitle,
        type: this.selected,
        placeholder: this.placeholderInputTypes.includes(this.selected) ? this.placeholderTitle : '',
				description: this.additionalDescription.trim(),
        options: this.optionInputTypes.includes(this.selected)
          ? this.inputInnerOptions
          : [],
        checkedOptions: [],
        isRequired: this.isRequired,
        value: this.selected == 'toggle' ? false : ''
			}

			this.$emit('addInput', newInputField);

      //Reset Values
      this.selected = 'text';
      this.inputInnerOptions = ['Option'],
      this.labelTitle = '';
      this.placeholderTitle = '';
      this.isRequired = false;
    },
    addInnerOption() {
      this.inputInnerOptions.push(`Option`);
    },
    removeOption(index) {
      this.inputInnerOptions.splice(index, 1);
    },
		updateRequiredStatus(state) {
      this.isRequired = state;
    },
	},
}
</script>
<style scoped>

</style>