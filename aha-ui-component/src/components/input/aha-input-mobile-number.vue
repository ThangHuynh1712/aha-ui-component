<template>
  <imask-input
    v-bind="$attrs"
    v-model="localValue"
    :mask="phoneFormatMask"
    :signed="false"
    :scale="0"
    :min="0"
    :max="maxLength"
    class="aha-input-mobile-number"
    v-on="mergedListeners"
    @paste.native="onPasteNative"
    @keydown.native="onKeydownNative"
  />
</template>

<script>
// We will create this component in order to prepare for format mobile phone by country case in next Releases

// Utils
import { CLIENT_DATA_RULES } from '../../helper/system-data-rules'

// Components
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
    maxLength: {
      type: Number,
      default: CLIENT_DATA_RULES.MAX_MOBILE_NUMBER_LENGTH,
      validator: propValue => Number(propValue).toString().length > 0
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
        this.$emit('input', newValue)
      },
    },
    phoneFormatMask() {
      // format mobile phone by diferrent country in next Releases
      let formatMask = ''
      if (this.maxLength !== undefined && this.maxLength !== null) {
        for (let index = 0; index < this.maxLength || 0; index++) {
          formatMask += '0'
        }
      }

      return formatMask
    },
    regexRule() {
      return /^\d+$/
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
        !isPasteShortut&&
        !isTab
      ) {
        event.preventDefault()
      }
    },
    onPasteNative(event) {
      const pastedValue = (event.clipboardData || window.clipboardData).getData('text')
      if (!this.regexRule.test(pastedValue)) {
        event.preventDefault()
      }
    },
    autoSelectInput() {
      this.$nextTick(() => {
        if (this.$el) {
          this.$el.select()
        }
      })
    },
  },
}
</script>

<style lang="scss" scoped>
.aha-input-mobile-number {
  border-radius: 0;
}
</style>