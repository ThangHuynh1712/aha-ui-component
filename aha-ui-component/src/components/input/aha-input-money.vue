<template>
  <aha-input-number
    v-if="!isNolimit"
    v-bind="$attrs"
    v-model="localValue"
    :min-value="minValue"
    :max-length="maxLength"
    thousands-separator=","
    class="aha-input-money"
    v-on="mergedListeners"
  />
  <div v-else class="aha-input-money is-no-limit" />
</template>

<script>
// Utils
import { COMMON_DATA_RULES } from '../../helper/system-data-rules'

// Components
import AhaInputNumber from './aha-input-number.vue'

export default {
  components: {
    AhaInputNumber,
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
      default: COMMON_DATA_RULES.MAX_INPUT_NUMBER_LENGTH,
    },
    minValue: {
      type: Number,
      default: 0,
    },
     isNolimit: {
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
        this.$emit('input', newValue)
      },
    },
  },
  watch: {
    isNolimit(newValue, oldValue){
      if(newValue === false && newValue !== oldValue && this.$attrs.value === options.enum_no_limit){
        this.$emit('input', '')
      }
    }
  },
  methods: {
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
.aha-input-money {
  border: 1px solid $gray;
  outline: none;
  &.is-no-limit {
    height: 25px;
    background: $gray-disabled;
  }
}
</style>