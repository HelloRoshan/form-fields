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
            <label for="label-title ">Enter Label for Input Type</label>
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
              v-model.trim="labelTitle"
              :required="true"
              type="text"></b-form-input>
          </b-col>
          <!-- TODO: Add If required Toggle -->
          <template v-if="optionInputTypes.includes(selected)">
            <b-col sm="12" class="p-0 mr-2 mt-2 mb-2">
              <template v-for="(inputInnerOption, index) in inputInnerOptions">
                <b-row :key="index" class="mt-2 mb-3">
                  <b-col sm="2">
                    <template v-if="selected == 'checkbox'">
                      x
                    </template>
                    <template v-else-if="selected == 'radio'">
                      o
                    </template>
                    <template v-else>
                      {{index + 1}}.
                    </template>
                  </b-col>
                  <b-col sm="8">
                    <b-form-input
                      v-model.trim="inputInnerOptions[index]"
                      type="text"></b-form-input>
                  </b-col>
                  <b-col sm="2">
                    <!-- TODO: Show on Hover -->
                    <span @click="removeOption(index)" style="color:red;">X</span>
                  </b-col>
                </b-row>
              </template>
            </b-col>
            <b-col sm="12" class="p-0 mr-2 mt-2 mb-2">
              <b-button variant="success" @click="addInnerOption">Add Option</b-button>
            </b-col>
          </template>
          
          <b-col sm="12" class="p-0 mr-2 mt-2 mb-2" v-if="selected  == 'toggle'">
            <b-row>
              <b-col sm="6">
                <label for="toggle-option-one">Option One</label>
                <b-form-input
                  id="toggle-option-one"
                  v-model.trim="toggleOptionOne"
                  :required="true"
                  type="text"></b-form-input>
              </b-col>
              <b-col sm="6">
                <label for="toggle-option-two">Option Two</label>
                <b-form-input
                  id="toggle-option-two"
                  v-model.trim="toggleOptionTwo"
                  :required="true"
                  type="text"></b-form-input>
              </b-col>
            </b-row>
          </b-col>
        </b-row>
      </b-card>
      
      <!-- TODO: Add possible checks for each inputtype -->
      <b-row class="m-0 mt-2">
        <b-button variant="success" :disabled="!labelTitle" @click="addInput">Add Input</b-button>
      </b-row>
      <b-card class="mt-4 mb-4" align="left">
        <b-row v-for="(inputType, index) in inputTypeList" :key="inputType.id" class="mb-3">
          <b-col sm="3">
            <label :for="'input-'+index">{{inputType.label}} :</label>
          </b-col>
          <b-col :sm="9">
            <template v-if="['text','date','time'].includes(inputType.type)">
              <b-form-input :id="'input-'+index" :type="inputType.type" :placeholder="inputType.placeholder"></b-form-input>
            </template>
            <template v-else-if="inputType.type == 'textarea'">
              <b-form-textarea rows="3" :placeholder="inputType.placeholder"></b-form-textarea>
            </template>
            <template v-else-if="inputType.type == 'checkbox'"></template>
            <template v-else-if="inputType.type == 'radio'"></template>
            <template v-else-if="inputType.type == 'select'"></template>
            <template v-else-if="inputType.type == 'toggle'"></template>
            <template v-else-if="inputType.type == 'datetime'"></template>
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

export default {
  name: 'App',
  data() {
    return {
      options: [
        {text: "Text", value: 'text'},
        {text: "Text Area", value: 'textarea'}, // Exception need to use Textarea component
        {text: "CheckBox",value: 'checkbox'}, // Exception
        {text: "Radio Button", value: 'radio'},// Exception
        {text: "Select Dropdown", value: 'select' },// Exception
        {text: "Toggle Switch", value: 'toggle'},// Exception
        {text: "Date", value: 'date'},
        {text: "Time", value: 'time'},
        {text: "Date-Time", value: 'datetime'},// Exception
      ],
      placeholderInputTypes: ['text', 'textarea'],
      optionInputTypes: ['checkbox', 'radio', 'select'],
      inputInnerOptions: ['Option'],
      labelTitle: '',
      selected: 'text',
      inputTypeList: [
        /* Test Data */
        // {
        //   label: 'Name',
        //   type: 'text',
        //   id: 0
        // }
        // Format
        /* {
          label: ,
          type: ,
          placeholder: ,
        } */
      ], // should have label, type, placeholder*, options: [], remove option, min max
      toggleOptionOne: '',
      toggleOptionTwo: ''
    }
  },
  watch: {
    selected() {
      // reset internal options and values
    }
  },
  mounted() {
    document.title = 'Form Field';
  },
  methods: {
    addInput() {
      this.inputTypeList.push({
        label: this.labelTitle,
        type: this.selected
      });
      this.selected = null;
      this.labelTitle = '';
    },
    addInnerOption() {
      this.inputInnerOptions.push(`Option`);
    },
    removeOption(index) {
      this.inputInnerOptions.splice(index, 1);
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
</style>
