<script>
  import axios from "axios";
  import { onMount } from "svelte";
  import * as yup from "yup";
  import Navbar from "./Navbar.svelte";

  let formList = [];
  let selectedFormId = "";
  let selectedForm = null;
  let loading = false;
  let error = null;
  let formData = {};
  let errors = {};

  const validationSchemas = {
    name: yup
      .string()
      .min(3, "Name must be at least 3 characters")
      .max(15, "Name must not exceed 15 characters")
      .required("Name is required"),
    phone: yup
      .string()
      .matches(/^\d{10}$/, "Phone number must be exactly 10 digits")
      .required("Phone number is required"),
    dob: yup
      .date()
      .max(new Date(), "Date of Birth cannot be a future date")
      .required("Date of Birth is required"),
    email: yup
      .string()
      .email("Invalid email format")
      .required("Email is required"),
    aadhar: yup
      .string()
      .matches(/^\d{12}$/, "Aadhaar number must be exactly 12 digits")
      .required("Aadhaar number is required"),
    textarea: yup
      .string()
      .min(10, "Text must be at least 10 characters")
      .max(500, "Text must not exceed 500 characters")
      .required("Text is required"),
  };

  let activeValidationSchema = null;

  const fetchForms = async () => {
    try {
      const response = await axios.get("http://localhost:8000/api/forms");
      formList = response.data;
    } catch (err) {
      error = "Failed to fetch forms";
    }
  };

  const fetchFormById = async (id) => {
    try {
      loading = true;
      const response = await axios.get(`http://localhost:8000/api/forms/${id}`);
      selectedForm = response.data;

      formData = selectedForm.fields.reduce((acc, field) => {
        acc[field.properties.name] = "";
        return acc;
      }, {});

      const schemaFields = selectedForm.fields.reduce((acc, field) => {
        if (validationSchemas[field.properties.name]) {
          acc[field.properties.name] = validationSchemas[field.properties.name];
        }
        return acc;
      }, {});

      activeValidationSchema = yup.object().shape(schemaFields);
    } catch (err) {
      error = "Failed to fetch form details";
    } finally {
      loading = false;
    }
  };

  const handleFormChange = (event) => {
    selectedFormId = event.target.value;
    if (selectedFormId) {
      fetchFormById(selectedFormId);
    } else {
      selectedForm = null;
    }
  };

  const validateField = async (name, value) => {
    try {
      if (validationSchemas[name]) {
        const fieldSchema = yup
          .object()
          .shape({ [name]: validationSchemas[name] });
        await fieldSchema.validate({ [name]: value });
        errors[name] = "";
      }
    } catch (validationError) {
      errors[name] = validationError.message;
    }
  };

  const handleInputChange = async (event) => {
    const { name, value } = event.target;
    formData[name] = value;
    await validateField(name, value);
  };

  const validateForm = async () => {
    try {
      await activeValidationSchema.validate(formData, { abortEarly: false });
      errors = {};
      return true;
    } catch (validationErrors) {
      errors = validationErrors.inner.reduce((acc, error) => {
        acc[error.path] = error.message;
        return acc;
      }, {});
      return false;
    }
  };

  const handleSubmit = async (event) => {
    event.preventDefault();
    const isValid = await validateForm();
    if (isValid) {
      console.log("Form submitted successfully!", formData);
    } else {
      console.error("Validation failed:", errors);
    }
  };

  onMount(() => {
    fetchForms();
  });
</script>

<main class="min-h-screen flex">
  <Navbar />  
  <!-- Left side image with border -->
  <div
    class="w-1/2 border border-indigo-500 flex items-center justify-center bg-gray-50"
  >
    <img
      src="/image.jpg"
      aria-hidden="true"
      alt="Template Image"
      class="max-h-full max-w-full object-cover"
    />
  </div>

  <!-- Right side dynamic form with border -->
  <div class="w-1/2 p-8 bg-gray-100">
    <h1 class="text-3xl font-bold text-indigo-700 mb-8 text-center">
      Dynamic Form
    </h1>

    {#if error}
      <div
        class="bg-red-50 border border-red-200 text-red-600 p-4 rounded mb-6 shadow-md"
      >
        {error}
      </div>
    {/if}

    <!-- Dropdown to select form -->
    <div class="mb-8 max-w-md mx-auto">
      <label
        for="form-select"
        class="block text-sm font-medium text-gray-700 mb-2"
      >
        Select a Form
      </label>
      <select
        id="form-select"
        class="block w-full p-3 border border-gray-300 rounded-lg shadow focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 transition"
        bind:value={selectedFormId}
        on:change={handleFormChange}
      >
        <option value="" disabled selected>-- Select a Form --</option>
        {#each formList as form (form._id)}
          <option value={form._id}>{form.formName}</option>
        {/each}
      </select>
    </div>

    <!-- Display form fields dynamically -->
    <div class="max-w-3xl mx-auto bg-white rounded-lg shadow p-6 border">
      {#if loading}
        <div class="text-gray-500 text-center">Loading...</div>
      {:else if selectedForm}
        <form class="space-y-6" on:submit={handleSubmit}>
          {#each selectedForm.fields as field (field._id)}
            <div>
              <label
                for={field.properties.name}
                class="block text-sm font-medium text-gray-700 mb-2"
              >
                {field.properties.label}
              </label>
              <input
                id={field.properties.name}
                type={field.attributes.type}
                placeholder={field.attributes.placeholder}
                name={field.properties.name}
                bind:value={formData[field.properties.name]}
                class="block w-full p-3 border border-gray-300 rounded-lg shadow focus:outline-none focus:ring-2 focus:ring-indigo-500"
                on:input={handleInputChange}
              />
              {#if errors[field.properties.name]}
                <p class="text-sm text-red-600 mt-2">
                  {errors[field.properties.name]}
                </p>
              {/if}
            </div>
          {/each}
          <button
            type="submit"
            class="w-full bg-indigo-600 text-white p-3 rounded-lg shadow hover:bg-indigo-700 transition"
          >
            Submit
          </button>
        </form>
      {/if}
    </div>
  </div>
</main>
