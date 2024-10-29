<template>
  <div>
    <label for="field">
      <slot>Domyślna etykieta</slot>
    </label>
    <input
      :type="inputType"
      id="field"
      v-model="inputValue"
      @input="validateInput"
      :class="{ 'input-error': hasError }"
    />
    <button @click="clearInput">Wyczyść</button>
    <div v-if="hasError" id="error-info">
      <p class="error-message">
        Wprowadź co najmniej {{ minCharacters }}
        <span v-if="minCharacters > 1">znaki</span>
        <span v-else>znak</span>
      </p>
    </div>
    <div
      v-if="inputType === 'number' && (hasMinError || hasMaxError)"
      id="error-info"
    >
      <p class="error-message" v-if="hasMinError">
        Wartość musi być większa lub równa {{ minValue }}.
      </p>
      <p class="error-message" v-if="hasMaxError">
        Wartość musi być mniejsza lub równa {{ maxValue }}.
      </p>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, computed } from "vue";

export default defineComponent({
  name: "FieldComponent",
  props: {
    minCharacters: {
      type: Number,
      default: 3,
    },
    inputType: {
      type: String,
      default: "text",
      validator: (value: string) => ["text", "number"].includes(value),
    },
    minValue: {
      type: Number,
      default: null,
    },
    maxValue: {
      type: Number,
      default: null,
    },
  },
  emits: ["input-change", "input-error"],
  setup(props, { emit }) {
    const inputValue = ref("");

    const validateInput = () => {
      emit("input-change", inputValue.value);

      if (
        inputValue.value.length > 0 &&
        inputValue.value.length < props.minCharacters
      ) {
        emit(
          "input-error",
          `Wprowadź co najmniej ${props.minCharacters} znaków.`
        );
      }

      if (props.inputType === "number") {
        const numericValue = parseFloat(inputValue.value);
        if (props.minValue !== null && numericValue < props.minValue) {
          emit(
            "input-error",
            `Wartość musi być większa lub równa ${props.minValue}.`
          );
        } else if (props.maxValue !== null && numericValue > props.maxValue) {
          emit(
            "input-error",
            `Wartość musi być mniejsza lub równa ${props.maxValue}.`
          );
        }
      }
    };

    const clearInput = () => {
      inputValue.value = "";
      validateInput();
    };

    const hasError = computed(() => {
      return (
        inputValue.value.length > 0 &&
        inputValue.value.length < props.minCharacters
      );
    });

    const hasMinError = computed(() => {
      if (props.inputType === "number" && props.minValue !== null) {
        return parseFloat(inputValue.value) < props.minValue;
      }
      return false;
    });

    const hasMaxError = computed(() => {
      if (props.inputType === "number" && props.maxValue !== null) {
        return parseFloat(inputValue.value) > props.maxValue;
      }
      return false;
    });

    return {
      inputValue,
      validateInput,
      clearInput,
      hasError,
      hasMinError,
      hasMaxError,
    };
  },
});
</script>

<style scoped>
.input-error {
  border: 2px solid red !important;
  outline: none;
}
.error-message {
  color: red;
  font-size: 14px;
  margin-top: 5px;
}
#error-info {
  display: flex;
  flex-direction: column;
  justify-content: center;
}
</style>
