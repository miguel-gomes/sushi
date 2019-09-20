<template>
  <div class="number-input">
    <div class="number-input__modifier" @click="increase">
      <img src="../assets/up-arrow.svg" />
    </div>
    <input
      :class="['number-input__field', {'number-input__field--large': large}]"
      type="text"
      ref="input"
      :value="value"
      @input="onInput($event.target.value)"
      @focus="focusInput"
      @blur="blurInput"
      @keydown.up="increase"
      @keydown.down="decrease"
    />
    <div class="number-input__modifier" @click="decrease">
      <img src="../assets/down-arrow.svg" />
    </div>
  </div>
</template>

<script>
export default {
  name: "NumberInput",
  props: {
    large: {
      type: Boolean,
      default: false
    },
    min: {
      type: Number,
      default: 0
    },
    max: {
      type: Number,
      default: 99
    },
    value: {
      type: Number,
      default: 0
    }
  },
  computed: {
    size() {
      return this.max.toString().length;
    }
  },
  mounted() {
    this.blurInput();
  },
  methods: {
    formatValue(value) {
      let formattedValue = "";

      value
        .toString()
        .split("")
        .forEach(char => {
          if (!isNaN(char)) {
            formattedValue = formattedValue.toString().concat(char);
          }

          formattedValue = Number(formattedValue);

          if (
            formattedValue > this.max ||
            formattedValue < this.min ||
            formattedValue.length > this.size
          ) {
            formattedValue = formattedValue
              .toString()
              .substring(0, formattedValue.toString().length - 1);
          }
        });

      return Number(formattedValue);
    },
    focusInput() {
      this.$refs.input.value = this.formatValue(this.$refs.input.value);

      setTimeout(() => {
        this.$refs.input.select();
      }, 0);
    },
    onInput(value) {
      let formattedValue = this.formatValue(value);

      this.$refs.input.value = formattedValue;
      this.$emit("input", formattedValue);
    },
    blurInput() {
      while (this.$refs.input.value.length < this.size) {
        this.$refs.input.value = "0" + this.$refs.input.value;
      }
    },
    increase() {
      if (Number(this.value) < this.max) {
        this.onInput(Number(this.value) + 1);
      }

      this.$nextTick(() => {
        this.blurInput();
      });
    },
    decrease() {
      if (Number(this.value) > this.min) {
        this.onInput(Number(this.value) - 1);
      }

      this.$nextTick(() => {
        this.blurInput();
      });
    }
  }
};
</script>

<style lang="scss" scoped>
.number-input {
  display: flex;
  flex-direction: column;
  flex-flow: column;
  flex-wrap: wrap;

  &__field {
    display: inline-block;
    width: 55px;
    height: 45px;
    font-size: 35px;
    text-align: center;
    border-radius: 4px;
    border: none;
    color: #424242;
    font-weight: 300;

    &--large {
      width: 80px;
    }
  }

  &__modifier {
    width: 15px;
    margin: 0 auto;
    cursor: pointer;
  }
}
</style>