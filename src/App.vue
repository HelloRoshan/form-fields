<template>
  <div id="app">
    <b-container>
      <b-card bg-variant="light">
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
        <b-row class="m-0">
          <b-col sm="4" class="mt-2 mb-2">
            <label for="label-title ">Enter Label for Input</label>
            <b-form-input
              id="label-title"
              v-model.trim="labelTitle"
              :required="true"
              :state="labelTitle.trim().length ? true : false"
              type="text"></b-form-input>
          </b-col>
          <b-col sm="4" class="mt-2 mb-2">
            <label for="input-type-selection">Select an Input Type</label>
            <b-form-select
              id="input-type-selection"
              v-model="selected"
              :options="options"></b-form-select>
          </b-col>
          <b-col sm="4" class="mt-2 mb-2" v-if="placeholderInputTypes.includes(selected)">
            <label for="label-title ">Enter Placeholder for Input Type</label>
            <b-form-input
              id="label-title"
              v-model.trim="placeholderTitle"
              :required="true"
              type="text"></b-form-input>
          </b-col>
          <b-col sm="4" class="mt-2 mb-2" v-if="selected != 'toggle'">
            <b-row class="m-0">
              <b-col sm="12" class="p-0"><label>Required Field:</label></b-col>
              <b-col sm="12" class="p-0"><ToggleSwitch :default-state="isRequired" @change="updateRequiredStatus"/></b-col>
            </b-row>
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
                      type="text"></b-form-input>
                  </b-col>
                  <b-col sm="1" style="margin: auto;cursor: pointer;" v-if="inputInnerOptions.length > 1">
                    <BIconTrash  @click="removeOption(index)" style="color:red;" v-b-popover.hover.top="'Delete Option'"/>
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
      </b-card>

      <b-card
        bg-variant="light"
        border-variant="info"
        class="mt-4 mb-4"
        align="left">
        <template #header>
          <b-row>
            <b-col>
              <header class="card-top-header">Created Form Fields</header>
            </b-col>
            <b-col>
              <b-button
                pill
                variant="success"
                size="sm"
                v-b-modal="'form-data-preview'"
                @click="previewForm"
                v-if="inputTypeList.length"
                style="position: absolute; right: 1.25rem;">Preview Form Data</b-button>
            </b-col>
          </b-row>
        </template>

        <b-row v-for="(inputType, index) in inputTypeList" :key="index" class="m-2 pb-4 pt-4 form-input-row">
          <b-col sm="12" class="p-2">
            <label
              :for="'input-'+index">
              {{index + 1  +')&nbsp;'}}{{inputType.label}}
            </label>
          </b-col>
          <b-col sm="12">
            <template v-if="['text','date','time'].includes(inputType.type)">
              <b-form-input
                :required="inputType.isRequired"
                :id="'input-'+index"
                :type="inputType.type"
                :placeholder="inputType.placeholder"
                v-model.trim="inputTypeList[index].value"
                :state="!inputType.isRequired ? null : inputTypeList[index].value != '' ? true : false"
                class="input-width mb-2"></b-form-input>
            </template>
            <template v-else-if="inputType.type == 'textarea'">
              <b-form-textarea
                rows="5"
                no-resize
                :required="inputType.isRequired"
                :placeholder="inputType.placeholder"
                :state="!inputType.isRequired ? null : inputTypeList[index].value != '' ? true : false"
                v-model.trim="inputTypeList[index].value"
                class="input-width mb-2"></b-form-textarea>
            </template>
            <template v-else-if="inputType.type == 'checkbox'">
              <b-form-checkbox-group
                v-model="inputTypeList[index].checkedOptions"
                :state="!inputType.isRequired ? null : inputTypeList[index].checkedOptions.length ? true : false">
                <b-form-checkbox
                  v-for="(option, index1) in inputType.options"
                  :key="index1"
                  :value="option"
                  class="mb-2 input-width">{{option}}</b-form-checkbox>
                  <b-form-invalid-feedback :state="!inputType.isRequired ? null : inputTypeList[index].checkedOptions.length ? true : false">
                    Please select one or more options
                  </b-form-invalid-feedback>
              </b-form-checkbox-group>
            </template>
            <template v-else-if="inputType.type == 'radio'">
              <b-form-radio
                v-for="(option, index1) in inputType.options"
                :key="index +'radio'+index1"
                :name="index+'radio-button'"
                :value="option"
                :state="!inputType.isRequired ? null : inputTypeList[index].value != '' ? true : false"
                v-model="inputTypeList[index].value"
                class="mb-2 input-width"
              >{{option}}</b-form-radio>
              <b-form-invalid-feedback :state="!inputType.isRequired ? null : inputTypeList[index].value != '' ? true : false">
                Please select one option
              </b-form-invalid-feedback>
            </template>
            <template v-else-if="inputType.type == 'select'">
              <b-form-select
                :options="inputType.options"
                v-model="inputTypeList[index].value"
                :state="!inputType.isRequired ? null : inputTypeList[index].value != '' ? true : false"
                class="mb-2 input-width"></b-form-select>
            </template>
            <template v-else-if="inputType.type == 'toggle'">
              <ToggleSwitch @change="(value) => $set(inputTypeList[index], 'value', value)" />
            </template>
          </b-col>
        </b-row>
        <b-row v-if="!inputTypeList.length" class="m-0">
          <p class="no-input-field-text">No Input Fields. Add Some New Fields</p>
        </b-row>
      </b-card>
      <b-modal id="form-data-preview" size="xl" title="Form Data Table" hide-footer>
        <b-table striped hover bordered :items="previewTableData"></b-table>
      </b-modal>
    </b-container>
  </div>
</template>

<script>
import { BIconCircle, BIconCheckSquare, BIconTrash, BIconPlus } from 'bootstrap-vue';
import ToggleSwitch from './components/ToggleSwitch.vue';

export default {
  name: 'App',
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
      selected: 'text',
      inputTypeList: [],
      isRequired: false
    }
  },
  computed: {
    inputValidityCheck() {
      return !this.labelTitle ||
        (this.optionInputTypes.includes(this.selected) &&
          (!this.inputInnerOptions.length ||
            this.inputInnerOptions.some(opt => !!opt.trim() ==  false)));
    },
    previewTableData() {
      return this.inputTypeList.map(inputType => {
        return {
          label: inputType.label,
          type: inputType.type,
          is_required: inputType.isRequired,
          placeholder: this.placeholderInputTypes.includes(inputType.type) ? inputType.placeholder : 'N/A',
          given_options: ['checkbox', 'radio', 'select'].includes(inputType.type) ? inputType.options.join(', ') : 'N/A',
          selected_options: inputType.type == 'checkbox' ? inputType.checkedOptions.join(', ') : inputType.type == 'radio' ? inputType.value : 'N/A',
          entered_input_value: ['checkbox', 'radio'].includes(inputType.type) ? 'N/A' : inputType.value
        }
      })
    }
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
  mounted() {
    document.title = 'Form Field';
  },
  methods: {
    addInput() {
      if(this.optionInputTypes.includes(this.selected)) {
        this.inputInnerOptions = this.inputInnerOptions.filter(opt => !!opt.trim())
      }
      this.inputTypeList.push({
        label: this.labelTitle,
        type: this.selected,
        placeholder: this.placeholderInputTypes.includes(this.selected) ? this.placeholderTitle : '',
        options: this.optionInputTypes.includes(this.selected)
          ? this.inputInnerOptions
          : [],
        checkedOptions: [],
        isRequired: this.isRequired,
        value: this.selected == 'toggle' ? false : '',
      });

      // Notification
      this.$bvToast.toast('New Input Field added to Form', {
        title: 'Input Field Created',
        autoHideDelay: 3000,
        variant: 'success',
        solid: true,
      });

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
    previewForm() {
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin-top: 60px;
}
label {
  font-weight: 700;
  font-size: .9rem;
}
.required-text {
  color:rgb(243, 55, 55);
  font-weight:200;
  font-size:.8rem;
  letter-spacing:.5px;
}
.form-input-row {
  border-bottom: 1px solid #ccc;
}
.form-input-row:last-child {
  border-bottom: none;
}
.input-width {
  max-width: 500px;
}
.no-input-field-text {
  font-size:.85rem;
  color: #999;
  width: 100%;
  text-align: center;
}
.card-top-header {
  font-weight: 700;
  font-size: 1.2rem;
}
.card-top-sub-header {
  font-size:.8rem;
  color: #999;
  font-weight:600;
}
</style>
