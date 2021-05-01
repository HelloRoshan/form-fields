<template>
  <div id="app">
    <b-container>
      <CreateInputFieldForm
        @addInput="addInput" />

      <b-card
        bg-variant="light"
        border-variant="success"
        class="mt-4 mb-4"
        header-class="sticky-top card-header-bg"
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
                v-if="inputTypeList.length"
                style="position: absolute; right: 1.25rem;">Preview Form Data</b-button>
            </b-col>
          </b-row>
        </template>

        <!-- Form Field Display -->
        <FormFieldsDisplay
          :inputTypeList="inputTypeList"
          @reorder="updateinputTypeList"
          @removeFormField="removeFormField" />

      </b-card>

      <!-- Modal: Data Preview in Tabular Form -->
      <b-modal id="form-data-preview" size="xl" title="Form Data Table" hide-footer>
        <b-table
          striped
          hover
          bordered
          :items="previewTableData"></b-table>
      </b-modal>
    </b-container>
  </div>
</template>

<script>
import CreateInputFieldForm from './components/CreateInputFieldForm.vue';
import FormFieldsDisplay from './components/FormFieldsDisplay.vue';

export default {
  name: 'App',
  components: {
    CreateInputFieldForm,
    FormFieldsDisplay,
  },
  data() {
    return {
      inputTypeList: [],
    }
  },
  computed: {
    previewTableData() {
      return this.inputTypeList.map(inputType => {
        return {
          label: inputType.label,
          given_options: ['checkbox', 'radio', 'select'].includes(inputType.type) ? inputType.options.join(', ') : 'N/A',
          selected_options: inputType.type == 'checkbox' ? inputType.checkedOptions.join(', ') : inputType.type == 'radio' ? inputType.value : 'N/A',
          entered_input_value: ['checkbox', 'radio'].includes(inputType.type) ? 'N/A' : inputType.value
        }
      })
    }
  },
  mounted() {
    document.title = 'Form Field';
  },
  methods: {
    addInput(newInputField) {
      this.inputTypeList.push(newInputField);

      // Notification
      this.$bvToast.toast('New Input Field added to Form', {
        title: 'Input Field Created',
        autoHideDelay: 2000,
        variant: 'success',
      });
    },
    removeFormField(index) {
      this.inputTypeList.splice(index, 1);
      this.$bvToast.toast('Input Field removed from Form', {
        title: 'Input Field Removed',
        autoHideDelay: 2000,
        variant: 'danger',
      });
    },
    updateinputTypeList(updatedArray) {
      this.inputTypeList = updatedArray;
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
.card-top-header {
  font-weight: 700;
  font-size: 1.2rem;
}
.card-top-sub-header {
  font-size:.8rem;
  color: #999;
  font-weight:600;
}
.card-header-bg {
  background-color: #f0f1f2 !important;
}
</style>
