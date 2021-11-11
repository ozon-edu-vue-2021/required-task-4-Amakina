<template>
  <div class="custom-input-wrapper">
    <label class="custom-input-wrapper__label">{{ label }}</label>
    <input
      class="custom-input-wrapper__input"
      type="text"
      :value="value"
      @input="$emit('input', $event.target.value)"
      :placeholder="placeholder"
      v-mask="mask"
    />
    <span class="custom-input-wrapper__error">{{ error }}</span>
  </div>
</template>

<script>
import { VueMaskDirective } from "v-mask";
export default {
  name: "CustomInput",
  props: {
    value: {
      type: String,
      default: "",
    },
    label: {
      type: String,
      default: "",
    },
    placeholder: {
      type: String,
      default: "",
    },
    error: {
      type: String,
      default: "",
    },
    mask: {
      type: String,
      default: "",
    },
  },
  directives: {
    mask: VueMaskDirective,
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
  padding: 0.5em;

  border: 1px solid #a2a2a2;
  border-radius: 10px;
}

.custom-input-wrapper__error {
  color: red;

  font-size: 12px;
  margin: 0.5em 0 0 0;
}
</style>
