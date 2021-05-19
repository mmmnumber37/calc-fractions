<template>
  <div class="fraction">
    <div v-if="viewClose" class="fraction__remove" @click="$emit('delete')" />

    <b-form-input
      v-model="numerator"
      :state="state"
      class="fraction__numerator text-center"
    />
    <hr class="fraction__line" />
    <b-form-input
      v-model="denominator"
      :state="state"
      class="fraction__denominator text-center"
    />
  </div>
</template>

<script>
export default {
  props: {
    viewClose: {
      type: Boolean,
      default: false,
    },
  },
  data() {
    return {
      numerator: null,
      state: null,
      denominator: null,
    }
  },
  watch: {
    numerator() {
      this.updatedFraction()
    },
    denominator() {
      this.updatedFraction()
    },
  },
  methods: {
    updatedFraction() {
      if (
        /^[1-9][0-9]?$/.test(this.numerator) &&
        /^[1-9][0-9]?$/.test(this.denominator)
      ) {
        this.$emit('input', {
          numerator: this.numerator,
          denominator: this.denominator,
        })
        this.state = null
      } else {
        this.$emit('input', {
          numerator: null,
          denominator: null,
        })
        this.state = false
      }
    },
  },
}
</script>

<style lang="scss">
.form-control.is-invalid {
  padding-right: 0.75rem;
  background: none;
}

.fraction {
  position: relative;
  display: flex;
  flex-direction: column;
  width: 50px;

  &__remove {
    position: absolute;
    width: 15px;
    height: 15px;
    top: -5px;
    right: -17px;

    &:before,
    &:after {
      position: absolute;
      content: ' ';
      left: 7px;
      height: 15px;
      width: 2px;
      background-color: #333;
    }
    &:before {
      transform: rotate(45deg);
    }
    &:after {
      transform: rotate(-45deg);
    }
  }

  &__line {
    border-color: #333;
  }
}
</style>
