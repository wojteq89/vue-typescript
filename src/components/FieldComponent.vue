<template>
  <div>
    <div id="row">
      <label for="field" id="label">
        <slot>Domyślna etykieta</slot>
      </label>
      <div class="inputs" :class="{ 'input-error': hasAnyError }">
        <input
          :type="inputType"
          class="field"
          v-model="inputValue"
          @input="validateInput"
        />
        <button id="button" @click="clearInput">Wyczyść</button>
      </div>
    </div>
    <div v-if="hasError" class="error-info fade-out">
      <p class="error-message">
        Wprowadź co najmniej {{ minCharacters }}
        <span v-if="minCharacters > 4">znaków</span>
        <span v-else>znaki</span>
      </p>
    </div>
    <div
      v-if="inputType === 'number' && (hasMinError || hasMaxError)"
      class="error-info fade-out"
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
        if (!isNaN(numericValue)) {
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
      }
    };

    const clearInput = () => {
      inputValue.value = "";
      validateInput();
    };

    const hasError = computed(() => {
      return (
        props.inputType === "text" &&
        inputValue.value.length > 0 &&
        inputValue.value.length < props.minCharacters
      );
    });

    const hasMinError = computed(() => {
      if (props.inputType === "number" && props.minValue !== null) {
        const numericValue = parseFloat(inputValue.value);
        return !isNaN(numericValue) && numericValue < props.minValue;
      }
      return false;
    });

    const hasMaxError = computed(() => {
      if (props.inputType === "number" && props.maxValue !== null) {
        const numericValue = parseFloat(inputValue.value);
        return !isNaN(numericValue) && numericValue > props.maxValue;
      }
      return false;
    });

    const hasAnyError = computed(
      () => hasError.value || hasMinError.value || hasMaxError.value
    );

    return {
      inputValue,
      validateInput,
      clearInput,
      hasError,
      hasMinError,
      hasMaxError,
      hasAnyError,
    };
  },
});
</script>

<style scoped>
@keyframes fade {
  from {
    opacity: 0;
    max-height: 0px;
  }
  to {
    max-height: 200px;
    opacity: 1;
  }
}

.fade-out {
  animation: fade 2s forwards;
}

.inputs {
  display: flex;
  flex-direction: column;
  border-radius: 15px;
  border: 2px solid transparent;
  transition: all 0.3s ease-in-out;
}
.input-error {
  border: 2px solid rgba(255, 0, 0, 0.514) !important;
  outline: none;
}
.error-message {
  color: rgb(255, 0, 0);
  font-size: 14px;
  margin-top: 5px;
}
.error-info {
  display: flex;
  flex-direction: column;
  justify-content: center;
}

#row {
  width: 100%;
  height: auto;
  display: flex;
  flex-direction: column;
  margin-bottom: 20px;
}

#label {
  font-size: 20px;
  text-align: left;
}

.field {
  font-size: 20px;
  padding: 5px;
  text-align: center;
  border: transparent;
  border-top-left-radius: 13px;
  border-top-right-radius: 13px;
  border-bottom: none;
  outline: none;
}

#button {
  font-size: 20px;
  padding: 5px;
  border-bottom-left-radius: 13px;
  border-bottom-right-radius: 13px;
  border: transparent;
  border-top: none;
  transition: all 0.3s ease-in-out;
  cursor: pointer;
}

#button:hover {
  background-color: rgba(255, 0, 0, 0.514);
}
</style>
