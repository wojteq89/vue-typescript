<template>
  <div>
    <h2>Formularz danych osobowych</h2>
    <form @submit.prevent="submitForm">
      <FieldComponent
        v-for="(field, index) in fields"
        :key="index"
        :minCharacters="field.minCharacters"
        :inputType="field.type"
        :minValue="field.minValue"
        :maxValue="field.maxValue"
        @input-change="handleInputChange"
        @input-error="handleInputError"
      >
        {{ field.label }}
      </FieldComponent>
      <button type="submit">Wyślij</button>
    </form>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref } from "vue";
import FieldComponent from "./FieldComponent.vue";

interface Field {
  label: string;
  type: string;
  minCharacters?: number;
  minValue?: number;
  maxValue?: number;
}

export default defineComponent({
  name: "FormComponent",
  components: {
    FieldComponent,
  },
  setup() {
    const fields: Field[] = [
      { label: "Imię", type: "text", minCharacters: 3 },
      { label: "Nazwisko", type: "text", minCharacters: 5 },
      { label: "Wiek", type: "number", minValue: 18, maxValue: 65 },
      { label: "Waga (kg)", type: "number", minValue: 50, maxValue: 100 },
      { label: "Miejscowość", type: "text", minCharacters: 7 },
    ];

    const formData = ref<{
      firstName: string;
      lastName: string;
      age: number | null;
      weight: number | null;
      city: string;
    }>({
      firstName: "",
      lastName: "",
      age: null,
      weight: null,
      city: "",
    });

    const handleInputChange = (index: number, value: string) => {
      switch (index) {
        case 0:
          formData.value.firstName = value;
          break;
        case 1:
          formData.value.lastName = value;
          break;
        case 2:
          formData.value.age = value ? parseFloat(value) : null;
          break;
        case 3:
          formData.value.weight = value ? parseFloat(value) : null;
          break;
        case 4:
          formData.value.city = value;
          break;
      }
    };

    const handleInputError = (errorMessage: string) => {
      console.error("Błąd:", errorMessage);
    };

    const submitForm = () => {
      console.log("Dane formularza:", formData.value);
    };

    return {
      fields,
      formData,
      handleInputChange,
      handleInputError,
      submitForm,
    };
  },
});
</script>

<style scoped>
form {
  display: flex;
  flex-direction: column;
  align-items: center;
}
button {
  margin-top: 20px;
}
</style>
