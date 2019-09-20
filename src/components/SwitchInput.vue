<template>
  <div class="switch" @click="toggle">
    <span class="switch__hover" :class="{'switch__hover--on': on}">{{ on ? '+' : '-'}}</span>
    <span class="switch__button">-</span>
    <span class="switch__button">+</span>
    <input class="hide" type="checkbox" :value="value" />
  </div>
</template>

<script>
export default {
  name: "SwitchInput",
  props: {
    value: {
      type: Boolean,
      default: true
    }
  },
  data() {
    return {
      on: this.value
    };
  },
  methods: {
    toggle() {
      this.on = !this.on;
      this.$emit("input", this.on);
    }
  }
};
</script>

<style lang="scss" scoped>
.switch {
  display: inline-block;
  font-size: 0;
  border: 2px solid #2992d0;
  background-color: #2992d0;
  border-radius: 20px;
  margin-right: 30px;
  position: relative;
  cursor: pointer;
  user-select: none;
  height: 30px;

  &__button {
    width: 30px;
    height: 30px;
    line-height: 30px;
    font-size: 1rem;
    display: inline-block;
    color: #fff;
    font-weight: bold;
  }

  &__hover {
    width: 30px;
    height: 30px;
    line-height: 30px;
    position: absolute;
    background-color: white;
    color: #2992d0;
    font-size: 1rem;
    font-weight: bold;
    border-radius: 20px;
    transition: right ease 0.2s;
    right: 30px;

    &--on {
      right: 0;
    }
  }
}
</style>