<template>
  <div class="custom-input-wrapper">
    <div class="custom-input-wrapper__label">{{ label }}</div>
    <div class="radio-buttons-container">
      <div
        v-for="option in options"
        :key="option.id"
        class="custom-input-wrapper__input"
      >
        <input
          :id="`${option.label}`"
          type="radio"
          v-model="selected"
          :value="option[optionValueName]"
          @change="handleSelect"
        />
        <label :for="`${option.label}`">{{ option[optionLabelName] }}</label>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "CustomRadioGroup",
  props: {
    value: {
      type: String,
      default: "",
    },
    label: {
      type: String,
      default: "",
    },
    optionValueName: {
      type: String,
      default: "value",
    },
    optionLabelName: {
      type: String,
      default: "label",
    },
    options: {
      type: Array,
      default: () => [],
    },
  },
  data() {
    return {
      selected: "",
    };
  },
  created() {
    this.selected = this.value;
  },
  methods: {
    handleSelect() {
      this.$emit("input", this.selected);
    },
  },
};
</script>

<style scoped>
.custom-input-wrapper {
  display: flex;
  flex-direction: column;
}

.custom-input-wrapper__label {
  margin: 0 0 0.5em 0;
}

.custom-input-wrapper__input {
  margin: 0 1em 0 0;
}

.radio-buttons-container {
  display: flex;
}
</style>
