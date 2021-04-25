<template>
  <div id="app">
    <b-container>
      <!-- TODO: Add Form Title and Description -->
      <b-row class="m-0">
        <b-col sm="4" class="p-0 mr-2 mt-2 mb-2">
          <label for="input-type-selection">Select an Input Type</label>
          <b-form-select
            id="input-type-selection"
            v-model="selected"
            :options="options"></b-form-select>
        </b-col>
        <b-col sm="5" class="p-0 mr-2 mt-2 mb-2">
          <label for="label-title ">Enter Label for Input Type</label>
          <b-form-input
            id="label-title"
            v-model.trim="labelTitle"
            :required="true"
            type="text"></b-form-input>
        </b-col>
        <b-col>
          <b-button variant="success" :disabled="!labelTitle" @click="addInput">Add Input</b-button>
        </b-col>
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
      labelTitle: '',
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
</style>
