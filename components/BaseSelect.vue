<template>
  <div class="relative flex items-center">
    <img v-if="returnData == ''" src="~/assets/img/icons/down-arrow.svg"  alt="arrow facing down" class="absolute right-0 mr-2 h-3 w-auto">
    <select
      v-model="returnData"
      :class="[
        touchInput ? 'main-border' : 'input-border',
        actualErr ? 'error-input' : '',
        width,
        returnData === '' ? 'inactive-text' : 'main-text',
      ]"
      class="flex items-center justify-center focus:outline-none px-2 text-sm py-2 z-10 bg-transparent"
      @focus="touchInput = true"
      @blur="checkInputUsername()"
    >
      <option class="inactive-text" :class="textCenter ? 'text-center' : ''" value="" disabled>{{ name }}</option>
      <option v-for="(smallData, i) in data" :key="i" class="main-text font-semibold">
        {{ smallData.level }}
      </option>
    </select>
  </div>
</template>

<script>
export default {
  props: {
    data: {
      type: Array,
      default: () => {
        return []
      },
    },
    width: {
      type: String,
      default: 'w-auto',
    },
    name: {
      type: String,
      default: 'data',
    },
    textCenter: {
      type: Boolean,
      default: false,
    },
    err: {
      type: Boolean,
      default: false
    },
  },
  data() {
    return {
      returnData: '',
      touchInput: false,
      inputErr: false,
    }
  },
  computed: {
      actualErr() {
        return this.err || this.inputErr
      }
    },
  methods: {
    updateValue(value) {
      this.$emit('dataSelected', value)
    },
    checkInputUsername() {
      if (this.returnData === '') {
        this.touchInput = false
        this.inputErr = true
        this.updateValue('nothing')
      } else {
        this.touchInput = false
        this.inputErr = false
        this.updateValue(this.returnData)
      }
    },
  },
}
</script>

<style scoped>
.input-border {
  border-radius: 8px;
  border: 1px solid #949292;
}
select {
    -moz-appearance:none; /* Firefox */
    -webkit-appearance:none; /* Safari and Chrome */
    appearance:none;
}

.input-border {
  border-radius: 8px;
  border: 1px solid #949292;
}

.main-border {
  border: 1px solid #396afc;
  border-radius: 8px;
}

.error-input {
  border-color: #ff0000 !important;
}
</style>
