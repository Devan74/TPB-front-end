<script>
  import * as yup from "yup";
  import axios from "axios";
  import { navigate } from "svelte-routing";

  let email = "";
  let password = "";
  let errors = { email: "", password: "" };
  const schema = yup.object().shape({
    email: yup
      .string()
      .email("Invalid email format")
      .required("Email is required"),
    password: yup
      .string()
      .matches(
        /^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)(?=.*[@$!%*?&])[A-Za-z\d@$!%*?&]{8,}$/,
        "Password must be at least 8 characters long and include at least one uppercase letter, one lowercase letter, one number, and one special character"
      )
      .required("Password is required"),
  });

  const handleSubmit = async (event) => {
    event.preventDefault();
    errors = { email: "", password: "" };

    try {
      await schema.validate({ email, password }, { abortEarly: false });
      const response = await axios.post(
        "http://localhost:8000/api/auth/login",
        {
          email,
          password,
        }
      );

      if (response.status === 200) {
        console.log("Logged in successfully:", response.data);
        localStorage.setItem("token", response.data.token);
        alert("Login successful!");

        navigate("/template-management");
      }
    } catch (validationErrors) {
      validationErrors.inner.forEach((error) => {
        errors[error.path] = error.message;
      });
    }
  };
</script>

<div class="bg-gray-100 flex justify-center items-center h-screen">
  <div class="w-1/2 h-screen hidden lg:block">
    <img
      src="/Rectangle 44.png"
      aria-hidden="true"
      alt="Placeholder Image"
      class="object-cover w-full h-full"
    />
  </div>

  <div class="lg:p-36 md:p-52 sm:p-20 p-8 w-full lg:w-1/2">
    <h1 class="text-2xl font-semibold mb-4">Login</h1>
    <form on:submit={handleSubmit}>
      <div class="mb-4">
        <label for="email" class="block text-gray-600">Email</label>
        <input
          type="email"
          id="email"
          bind:value={email}
          class="w-full border border-gray-300 rounded-md py-2 px-3 focus:outline-none focus:border-blue-500"
          autocomplete="off"
        />
        {#if errors.email}
          <p class="text-red-500 text-sm mt-1">{errors.email}</p>
        {/if}
      </div>

      <div class="mb-4">
        <label for="password" class="block text-gray-600">Password</label>
        <input
          type="password"
          id="password"
          bind:value={password}
          class="w-full border border-gray-300 rounded-md py-2 px-3 focus:outline-none focus:border-blue-500"
          autocomplete="off"
        />
        {#if errors.password}
          <p class="text-red-500 text-sm mt-1">{errors.password}</p>
        {/if}
      </div>

      <button
        type="submit"
        class="bg-blue-500 hover:bg-blue-600 text-white font-semibold rounded-md py-2 px-4 w-full"
      >
        Login
      </button>
    </form>
  </div>
</div>
