<template>
  <imask-input
    v-bind="$attrs"
    v-model="localValue"
    :mask="Number"
    :signed="!isPositiveOnly"
    :scale="decimal"
    :min="validMinValueProp"
    :max="max"
    :prepare="prepareFunction"
    :commit="commitFunction"
    :unmask="unmaskPropValue"
    radix="."
    class="aha-input-number"
    v-on="mergedListeners"
    @input.native="inputNative"
    @blur.native="onBlurNative"
    @paste.native="onPasteNative"
    @keydown.native="onKeydownNative"
  />
</template>

<script>
import { IMaskComponent } from 'vue-imask'

export default {
  components: {
    'imask-input': IMaskComponent,
  },
  inheritAttrs: false,
  props: {
    value: {
      type: [String, Number],
      // required: true,
      default: '',
    },
    decimal: {
      type: Number,
      default: null,
    },
    isPositiveOnly: {
      type: Boolean,
      default: true,
    },
    maxLength: {
      type: Number,
      default: null,
      validator: (propValue) => propValue > 0,
    },
    minValue: {
      type: Number,
      default: null,
    },
    maxValue: {
      type: Number,
      default: null,
    },
    hideZeroOnBlur: {
      type: Boolean,
      default: false,
    },
    allowEmpty: {
      type: Boolean,
      default: true,
    },
    acceptEmptyStringValue: { // using for allow emit empty string value of v-model
      type: Boolean,
      default: false,
    },
    preventInputZeroValue: {
      type: Boolean,
      default: false,
    },
    useStringVModel: {
      type: Boolean,
      default: false,
    },
  },
  computed: {
    mergedListeners() {
      // `Object.assign` merges objects together to form a new object
      const listeners = Object.assign(
        {},
        // We add all the listeners from the parent
        this.$listeners,
        // Then we can add custom listeners or override the
        // behavior of some listeners.
        {}
      )
      delete listeners.input
      return listeners
    },
    localValue: {
      get() {
        return this.value
      },
      set(newValue) {
        let nextNewValue = newValue
        if (this.acceptEmptyStringValue && newValue === 0 && this.$el) {
          nextNewValue = this.$el.value === '' ? '': '0'
        }
        if (this.acceptEmptyStringValue && nextNewValue && nextNewValue > 0) {
          nextNewValue = Number(nextNewValue)
        }
        if (this.useStringVModel) {
          nextNewValue = nextNewValue.toString()
        }

        this.$emit('input', nextNewValue)
      },
    },
    generatedValueByMaxLength() {
      let maxValue = null
      if (this.maxLength !== undefined && this.maxLength !== null) {
        maxValue = ''
        for (let index = 0; index < this.maxLength || 0; index++) {
          maxValue += '9'
        }
      }

      return maxValue
    },
    min() {
      if (this.minValue !== undefined && this.minValue !== null) {
        return this.minValue
      }

      return this.generatedValueByMaxLength === null
        ? null
        : -Math.abs(Number(this.generatedValueByMaxLength))
    },
    validMinValueProp() {
      if (this.isPositiveOnly && this.min < 0) {
        return 0
      }
      return this.min
    },
    max() {
      if (this.maxValue !== undefined && this.maxValue !== null) {
        return this.maxValue
      }

      return this.generatedValueByMaxLength === null
        ? null
        : Math.abs(Number(this.generatedValueByMaxLength))
    },
    regexRule() {
      return new RegExp(
        `^[${!this.isPositiveOnly ? '-' : ''}${this.decimal > 0 ? '\\.?' : ''}\\d]*$`
      )
    },
    unmaskPropValue() {
      // to cover empty string value case, we need to use prop: unmask="true"
      if (this.useStringVModel || this.acceptEmptyStringValue && this.localValue === '0' && this.$el && this.$el.value == 0) {
        return true
      }
      return 'typed'
    },
  },
  methods: {
    onKeydownNative(event) {
      const eventKey = event.key
      const isArrowLeft = eventKey === 'ArrowLeft'
      const isArrowRight = eventKey === 'ArrowRight'
      const isDeleteKey = eventKey === 'Delete'
      const isBackspaceKey = eventKey === 'Backspace'
      const isCopyShorcut = (event.ctrlKey || event.metaKey) && (eventKey === 'c' || eventKey === 'C') // COPY SHORTCUT
      const isPasteShortut = (event.ctrlKey || event.metaKey) && (eventKey === 'v' || eventKey === 'V') // PASTE SHORTCUT
      const isSelectAllShortcut = (event.ctrlKey || event.metaKey) && (eventKey === 'a' || eventKey === 'A') // SELECT ALL SHORTCUT
      const isTab = eventKey === 'Tab'
      if (
        !this.regexRule.test(eventKey) &&
        !isDeleteKey &&
        !isBackspaceKey &&
        !isArrowLeft &&
        !isArrowRight &&
        !isSelectAllShortcut &&
        !isCopyShorcut &&
        !isPasteShortut &&
        !isTab||
        (this.localValue === 0 &&
          this.$el.value !== '' &&
          this.$el.value == 0 &&
          eventKey == 0) // prevent multiple zeros: '00'
      ) {
        event.preventDefault()
      }
    },
    inputNative(event) {
      const inputValue = Number(event.target.value)
      if (!this.allowEmpty && event.target.value == '') {
        this.$el.value = '0'
      } else if (
        !isNaN(inputValue) &&
        inputValue != 0 &&
        event.target.value !== '-' &&
        this.localValue == 0
      ) {
        this.$el.value = inputValue // allow input in case leading with 0: '01', '02', ... ----> '1', '2'
      }
    },
    onPasteNative(event) {
      const pastedValue = (event.clipboardData || window.clipboardData).getData('text')
      if (!this.regexRule.test(pastedValue)) {
        event.preventDefault()
      }
    },
    onBlurNative() {
      if (this.hideZeroOnBlur && this.localValue === 0 && this.$el) {
        this.$el.value = ''
      }
    },
    prepareFunction(inputValue, masked) {
      if (
        !this.allowEmpty &&
        this.localValue === 0 &&
        !this.regexRule.test(inputValue)
      ) { // this will cover input event can not preventDefault on android keyboard
        return '0'
      }
      
      if (
        this.preventInputZeroValue &&
        inputValue == 0 &&
        masked.value == 0
      ) {
        // avoid input with 0 value
        return ''
      }
      return inputValue
    },
    commitFunction(inputValue, masked) {
      if (this.hideZeroOnBlur && inputValue == 0 && this.localValue == 0) {
        // only using this to force value to empty when hideZeroOnBlur = true.
        // Because typedValue will be 0 whether the masked._value is 0 or ''
        // NOT USING THIS TO MANUALLY ASSIGN OTHER CUSTOM VALUES
        // REF: https://imask.js.org/guide.html
        masked._value = ''
      }
    },
  },
}
</script>

<style lang="scss" scoped>
.aha-input-number {
  height: 25px;
  border-radius: 0;
}
</style>