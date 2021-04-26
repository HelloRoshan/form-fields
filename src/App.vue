<template>
  <div id="app">
    <b-container>
      <!-- TODO: Separate the Components -->
      <!-- TODO: Add Form Title and Description -->
      <b-card>
          <b-row class="m-0">
          <b-col sm="4" class="p-0 mr-2 mt-2 mb-2">
            <label for="input-type-selection">Select an Input Type</label>
            <b-form-select
              id="input-type-selection"
              v-model="selected"
              :options="options"></b-form-select>
          </b-col>
          <b-col sm="4" class="p-0 mr-2 mt-2 mb-2">
            <label for="label-title ">Enter Label for Input <span class="required-text">(* Required)</span></label>
            <b-form-input
              id="label-title"
              v-model.trim="labelTitle"
              :required="true"
              type="text"></b-form-input>
          </b-col>
          <b-col sm="4" class="p-0 mr-2 mt-2 mb-2" v-if="placeholderInputTypes.includes(selected)">
            <label for="label-title ">Enter Placeholder for Input Type</label>
            <b-form-input
              id="label-title"
              v-model.trim="placeholderTitle"
              :required="true"
              type="text"></b-form-input>
          </b-col>
          <b-col sm="4" class="p-0 mr-2 mt-2 mb-2">
            <b-row class="m-0">
              <b-col sm="12" class="p-0"><label>Required Field:</label></b-col>
              <b-col sm="12" class="p-0"><ToggleSwitch @change="updateRequiredStatus"/></b-col>
            </b-row>
          </b-col>
          <!-- TODO: Add If required Toggle -->
          <template v-if="optionInputTypes.includes(selected)">
            <b-col sm="12" class="p-0 mr-2 mt-2 mb-2">
              <template v-for="(inputInnerOption, index) in inputInnerOptions">
                <b-row :key="index" class="mt-2 mb-3">
                  <b-col sm="1" style="margin: auto;">
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
                  <b-col sm="6">
                    <b-form-input
                      v-model.trim="inputInnerOptions[index]"
                      type="text"></b-form-input>
                  </b-col>
                  <b-col sm="1" offset-sm="4" style="margin: auto;cursor: pointer;" v-if="inputInnerOptions.length > 1">
                    <!-- TODO: Show on Hover -->
                    <BIconTrash  @click="removeOption(index)" style="color:red;"/>
                  </b-col>
                  <b-col :sm="inputInnerOptions.length > 1 ? 4: 5">
                    <b-button variant="success" @click="addInnerOption" v-if="index + 1 == inputInnerOptions.length">Add New Option</b-button>
                  </b-col>
                </b-row>
              </template>
            </b-col>
          </template>
        </b-row>
      </b-card>
      
      <!-- TODO: Add possible checks for each inputtype -->
      <b-row class="m-0 mt-2">
        <b-button variant="success" :disabled="inputValidityCheck" @click="addInput">Add Input</b-button>
      </b-row>

      <b-card class="mt-4 mb-4" align="left">
        <b-row v-for="(inputType, index) in inputTypeList" :key="index" class="m-2 pb-4 pt-4 form-input-row">
          <b-col sm="12" class="p-2">
            <label :for="'input-'+index">{{index + 1  +')&nbsp;'}}{{inputType.label}} <span class="required-text" v-if="inputType.isRequired">(* Required)</span> :</label>
          </b-col>
          <b-col sm="12">
            <template v-if="['text','date','time'].includes(inputType.type)">
              <b-form-input :required="inputType.isRequired" :id="'input-'+index" class="input-width mb-2" :type="inputType.type" :placeholder="inputType.placeholder"></b-form-input>
            </template>
            <template v-else-if="inputType.type == 'textarea'">
              <b-form-textarea rows="3" :required="inputType.isRequired"  class="input-width mb-2" :placeholder="inputType.placeholder"></b-form-textarea>
            </template>
            <template v-else-if="inputType.type == 'checkbox'">
              <b-form-checkbox v-for="(option, index1) in inputType.options" :key="index1" class="mb-2 input-width">{{option}}</b-form-checkbox>
            </template>
            <template v-else-if="inputType.type == 'radio'">
              <b-form-radio
                v-for="(option, index1) in inputType.options"
                :key="index +'radio'+index1"
                :name="index+'radio-button'"
                class="mb-2 input-width"
              >{{option}}</b-form-radio>
            </template>
            <template v-else-if="inputType.type == 'select'">
              <b-form-select :options="inputType.options" class="mb-2 input-width"></b-form-select>
            </template>
            <template v-else-if="inputType.type == 'toggle'">
              <ToggleSwitch />
            </template>
          </b-col>
        </b-row>
        <b-row v-if="!inputTypeList.length" class="m-0">
          <p class="text-justify" style="font-size:.85rem;color: #999;">No Input Fields. Add Some New Fields</p>
        </b-row>
      </b-card>
    </b-container>
  </div>
</template>

<script>
import { BIconCircle, BIconCheckSquare, BIconTrash } from 'bootstrap-vue';
import ToggleSwitch from './components/ToggleSwitch.vue';

export default {
  name: 'App',
  components: {
    BIconCircle,
    BIconCheckSquare,
    BIconTrash,
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
        isRequired: this.isRequired
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
  max-width: 420px;
}
</style>
