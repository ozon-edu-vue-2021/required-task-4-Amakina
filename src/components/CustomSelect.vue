<template>
  <div class="custom-input-wrapper" v-click-outside="cancelSelect">
    <label class="custom-input-wrapper__label">{{ label }}</label>
    <input
      class="custom-input-wrapper__input"
      type="text"
      v-model="searchQuery"
      @focus="onSearchFocus"
      @input="onInputSearch"
    />
    <div class="dropdown" v-if="isShowDropdown">
      <ul class="dropdown-list">
        <li
          v-for="option in filteredOptions"
          :key="option.id"
          class="dropdown-list__item"
          @click="() => selectValue(option)"
        >
          {{ option[optionLabelName] }}
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
import ClickOutside from "vue-click-outside";

export default {
  name: "CustomSelect",
  props: {
    value: {
      type: String,
      default: "",
    },
    type: {
      type: String,
      default: "text",
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
  directives: {
    ClickOutside,
  },
  data() {
    return {
      searchQuery: "",
      isDropdownOpen: false,
      filteredOptions: [],
      searchTimeout: null,
    };
  },
  computed: {
    isShowDropdown() {
      return this.isDropdownOpen && this.filteredOptions.length;
    },
  },
  created() {
    this.setSearchQuery(this.value);
    this.setFilteredOptions(this.options);
  },
  methods: {
    hideDropdown() {
      this.isDropdownOpen = false;
    },
    showDropdown() {
      this.isDropdownOpen = true;
    },
    setSearchQuery(value) {
      this.searchQuery = value;
    },
    onSearchFocus() {
      this.showDropdown();
      this.setSearchQuery("");
      this.setFilteredOptions(this.options);
    },
    cancelSelect() {
      this.hideDropdown();
      this.setSearchQuery(this.value);
    },
    selectValue(option) {
      this.hideDropdown();
      this.setSearchQuery(option[this.optionValueName]);
      this.$emit("input", option[this.optionValueName]);
    },
    setFilteredOptions(options) {
      this.filteredOptions = options;
    },
    onInputSearch() {
      clearTimeout(this.searchTimeout);

      this.searchTimeout = setTimeout(() => {
        const regex = new RegExp(`^${this.searchQuery}.*`, "i");
        this.setFilteredOptions(
          this.options.filter((option) =>
            option[this.optionValueName].match(regex)
          )
        );
      }, 500);
    },
  },
};
</script>

<style scoped>
.custom-input-wrapper {
  display: flex;
  flex-direction: column;

  position: relative;
}

.custom-input-wrapper__label {
  margin: 0 0 0.5em 0;
}

.custom-input-wrapper__input {
  padding: 0.5em;

  border: 1px solid #a2a2a2;
  border-radius: 10px;
}

.dropdown {
  position: absolute;
  top: 4em;
  z-index: 1;

  width: 100%;
  max-height: 300px;
  overflow-y: scroll;

  border: 1px solid #a2a2a2;
  border-radius: 10px;

  background: #fff;
}

.dropdown-list {
  list-style: none;
  margin: 0;
  padding: 0.5em;
}

.dropdown-list__item {
  cursor: pointer;
  padding: 0.5em;
}

.dropdown-list__item:hover {
  background: #cecece;
}
</style>
