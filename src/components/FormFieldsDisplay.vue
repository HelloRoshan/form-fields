<template>
  <div>
    <SlickList
      lockAxis="y"
      v-model="sortableInputTypeList"
      :useDragHandle="true">
      <SlickItem
        v-for="(inputType, index) in sortableInputTypeList"
        :index="index"
        :key="inputType.type + index"
        class="form-input-row">
        <b-row
          class="m-2 pb-4 pt-4"
          @mouseover="hoverHandler(index)"
          @mouseleave="unHoverHandler()">
          <b-col
            sm="1"
            title="Re-order Form Field"
            v-handle
            class="input-field-drag-handler">
            <BIconList class="input-field-drag-handler-icon" />
          </b-col>
          <b-col sm="9">
            <b-row>
              <b-col sm="12" class="p-2">
                <label :for="'input-'+index" class="question-title">
                  {{index + 1  +')&nbsp;'}}{{inputType.label}}
                </label>
                <h6 class="question-description" v-if="inputType.description">{{inputType.description}}</h6>
              </b-col>
              <b-col sm="12">
                <template v-if="['text','date','time'].includes(inputType.type)">
                  <b-form-input
                    :required="inputType.isRequired"
                    :id="'input-'+index"
                    :type="inputType.type"
                    :placeholder="inputType.placeholder"
                    v-model.trim="inputTypeList[index].value"
                    :state="valueInputValidState(inputType, index)"
                    :class="['date', 'time'].includes(inputType.type) ? 'datetime-input-width' : ''"
                    class="input-width mb-2"></b-form-input>
                </template>
                <template v-else-if="inputType.type == 'textarea'">
                  <b-form-textarea
                    rows="5"
                    no-resize
                    :required="inputType.isRequired"
                    :placeholder="inputType.placeholder"
                    :state="valueInputValidState(inputType, index)"
                    v-model.trim="inputTypeList[index].value"
                    class="input-width mb-2"></b-form-textarea>
                </template>
                <template v-else-if="inputType.type == 'checkbox'">
                  <b-form-checkbox-group
                    v-model="inputTypeList[index].checkedOptions"
                    :state="optionsInputValidState(inputType, index)">
                    <b-form-checkbox
                      v-for="(option, index1) in inputType.options"
                      :key="index1"
                      :value="option"
                      class="mb-2 input-width">{{option}}</b-form-checkbox>
                      <b-form-invalid-feedback
                        :state="optionsInputValidState(inputType, index)">
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
                    :state="valueInputValidState(inputType, index)"
                    v-model="inputTypeList[index].value"
                    class="mb-2 input-width"
                  >{{option}}</b-form-radio>
                  <b-form-invalid-feedback :state="valueInputValidState(inputType, index)">
                    Please select one option
                  </b-form-invalid-feedback>
                </template>
                <template v-else-if="inputType.type == 'select'">
                  <b-form-select
                    :options="inputType.options"
                    v-model="inputTypeList[index].value"
                    :state="valueInputValidState(inputType, index)"
                    class="mb-2 input-width"></b-form-select>
                </template>
                <template v-else-if="inputType.type == 'toggle'">
                  <ToggleSwitch @change="(value) => $set(inputTypeList[index], 'value', value)" />
                </template>
              </b-col>
            </b-row>
          </b-col>
          <b-col sm="2" style="margin:auto;">
            <BIconTrash
              v-if="hoverIndex == index"
              @click="removeFormField(index)"
              class="form-field-trash-icon"
              v-b-popover.hover.top="'Delete Form Field'" />
          </b-col>
        </b-row>
      </SlickItem>
    </SlickList>

    <b-row v-if="!inputTypeList.length" class="m-0">
      <p class="no-input-field-text">No Input Fields. Add Some New Fields</p>
    </b-row>
  </div>
</template>
<script>
import { BIconTrash, BIconList } from 'bootstrap-vue';
import ToggleSwitch from './ToggleSwitch.vue';
import { SlickList, SlickItem } from 'vue-slicksort';
import { HandleDirective } from 'vue-slicksort';

export default {
    name: 'FormFieldsDisplay',
    components: {
      BIconTrash,
      BIconList,
      ToggleSwitch,
      SlickList,
      SlickItem
    },
    directives: { handle: HandleDirective },
    props: {
      inputTypeList: {
        type: Array,
        required: true
      }
    },
    data() {
      return {
        hoverIndex: null,
        sortableInputTypeList: this.inputTypeList
      }
    },
    watch: {
      sortableInputTypeList: {
        handler: function(newArray) {
          this.$emit('reorder', newArray);
        },
        immediate: true,
        deep: true
      }
    },
    methods: {
      hoverHandler(index) {
        this.hoverIndex = index;
      },
      unHoverHandler() {
        this.hoverIndex = null;
      },
      removeFormField(index) {
        this.$emit('removeFormField', index);
      },
      valueInputValidState(inputType, index) {
        return !inputType.isRequired ? null : this.inputTypeList[index].value != '' ? true : false;
      },
      optionsInputValidState(inputType, index) {
        return !inputType.isRequired ? null : this.inputTypeList[index].checkedOptions.length ? true : false
      },
    },
}
</script>
<style scoped>
.form-field-trash-icon {
  color:red;
  cursor:pointer;
  font-size:1.5rem;
}
.form-input-row {
  border-bottom: 1px solid #ccc;
}
.form-input-row:last-child {
  border-bottom: none;
}
.input-width {
  max-width: 75%;
}
.datetime-input-width {
  max-width: 40%;
}
.no-input-field-text {
  font-size:.85rem;
  color: #999;
  width: 100%;
  text-align: center;
}
.input-field-drag-handler {
  margin: auto;
  cursor: move;
}
.input-field-drag-handler-icon {
  color:#555;
  font-size: 1.5rem;
}
label.question-title {
  font-size: 1.1rem;
  margin-bottom: .3rem;
}
.question-description {
  color: #999;
  font-size: .8rem;
  font-weight: 500;
  font-family: 'Hind-Medium', Avenir, Helvetica, Arial, sans-serif;
}
</style>