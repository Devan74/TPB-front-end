<script>
  import { onMount } from "svelte";

  let formData = {
    name: "",
    email: "",
    message: "",
  };

  const handleSubmit = (event) => {
    event.preventDefault();
    console.log("Form Submitted:", formData);
    alert(`Form Submitted:\nName: ${formData.name}\nEmail: ${formData.email}`);
    formData = { name: "", email: "", message: "" };
  };
</script>

<div class="flex justify-center items-center">
  <form
    class="w-full max-w-md bg-white border shadow-md rounded-lg p-6 space-y-4"
    on:submit|preventDefault={handleSubmit}
  >
    <h2 class="text-2xl font-bold text-center text-indigo-600">Contact Us</h2>
    <div>
      <label for="name" class="block text-sm font-medium text-gray-700"
        >Name</label
      >
      <input
        id="name"
        type="text"
        placeholder="Your name"
        bind:value={formData.name}
        class="mt-1 block w-full p-2 border border-gray-300 rounded-md shadow-sm focus:ring-indigo-500 focus:border-indigo-500"
        required
      />
    </div>

    <div>
      <label for="email" class="block text-sm font-medium text-gray-700"
        >Email</label
      >
      <input
        id="email"
        type="email"
        placeholder="Your email"
        bind:value={formData.email}
        class="mt-1 block w-full p-2 border border-gray-300 rounded-md shadow-sm focus:ring-indigo-500 focus:border-indigo-500"
        required
      />
    </div>

    <div>
      <label for="message" class="block text-sm font-medium text-gray-700"
        >Message</label
      >
      <textarea
        id="message"
        rows="4"
        placeholder="Your message"
        bind:value={formData.message}
        class="mt-1 block w-full p-2 border border-gray-300 rounded-md shadow-sm focus:ring-indigo-500 focus:border-indigo-500"
      ></textarea>
    </div>

    <button
      type="submit"
      class="w-full py-2 px-4 bg-indigo-600 text-white rounded-md hover:bg-indigo-700 transition"
    >
      Submit
    </button>
  </form>
</div>
