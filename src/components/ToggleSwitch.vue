<template>
    <label class="switch">
        <input type="checkbox" v-model="checkedValue">
        <span class="slider round"></span>
    </label>
</template>
<script>
export default {
    name: 'ToggleSwitch',
    props: {
        defaultState: {
            type: Boolean,
            default: false
        }
    },
    data() {
        return {
            currentState: this.defaultState
        }
    },
    computed: {
        isActive() {
            return this.currentState;
        },
        checkedValue: {
            get() {
                return this.defaultState
            },
            set(newValue) {
                this.currentState = newValue;
                this.$emit('change', newValue);
            }
        }
    }
}
</script>
<style scoped>
.switch {
  position: relative;
  display: inline-block;
  width: 45px;
  height: 16px;
}

/* Hide default HTML checkbox */
.switch input {
  opacity: 0;
  width: 0;
  height: 0;
}

/* The slider */
.slider {
  position: absolute;
  cursor: pointer;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: #ccc;
  -webkit-transition: .4s;
  transition: .4s;
}

.slider:before {
  position: absolute;
  content: "";
  height: 20px;
  width: 20px;
  left: 0px;
  bottom: -2px;
  background-color: rgb(138, 129, 129);
  -webkit-transition: .4s;
  transition: .4s;
}

input:checked + .slider {
  background-color: #84d897;
}

input:focus + .slider {
  box-shadow: 0 0 1px #84d897;
}

input:checked + .slider:before {
  -webkit-transform: translateX(26px);
  -ms-transform: translateX(26px);
  transform: translateX(26px);
  background-color: #28a745;
}

/* Rounded sliders */
.slider.round {
  border-radius: 34px;
}

.slider.round:before {
  border-radius: 50%;
}
</style>
