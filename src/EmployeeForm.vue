<template>
  <form @submit.prevent="handleSubmit" id="employee-form">
    <label>Name</label>
    <input
      type="text"
      v-model="employee.name"
      @focus="clearStatus"
      @keypress="clearStatus"
      :class="{ 'has-error': submitting && invalidName }"
    />
    <label>Email</label>
    <input
      type="text"
      v-model="employee.email"
      @focus="clearStatus"
      @keypress="clearStatus"
      :class="{ 'has-error': submitting && invalidEmail }"
    />
    <button>Add Employee</button>
  </form>
  <p v-if="error && submitting" class="error-message">
    ❗Please fill out all required fields
  </p>
  <p v-if="success" class="success-message">✅ Employee successfully added</p>
</template>

<script>
import { computed, ref } from "vue";
export default {
  setup(props, { emit }) {
    let employee = ref({ name: "", email: "" });
    let submitting = ref(false);
    let error = ref(false);
    let success = ref(false);

    const invalidName = computed(() => employee.value.name === "");
    const invalidEmail = computed(() => employee.value.email === "");

    const clearStatus = () => {
      success.value = false;
      error.value = false;
    };

    const handleSubmit = () => {
      submitting.value = true;
      clearStatus();
      if (employee.value.name === "" || employee.value.email == "") {
        error.value = true;
        return;
      }

      fetch({ method: "POST", url: "/employee/create" }).then(() => {
        console.log("tried to POST employee data");
        emit("add:employee", employee.value);
        employee.value = { name: "", email: "" };
        error.value = false;
        submitting.value = false;
        success.value = true;
      });
    };

    return {
      employee,
      handleSubmit,
      clearStatus,
      submitting,
      error,
      success,
      invalidName,
      invalidEmail,
    };
  },
  emits: ["add:employee"],
};
</script>

<style scoped>
form {
  margin-bottom: 2rem;
}

[class*="-message"] {
  font-weight: 500;
}

.error-message {
  color: #d33c40;
}

.success-message {
  color: #32a95d;
}
</style>