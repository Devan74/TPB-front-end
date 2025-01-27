<script>
  import { onMount } from "svelte";
  import { Link } from "svelte-routing";
  import Navbar from "../components/Navbar.svelte";
  import { FormInput } from "lucide-svelte";

  let forms = [];
  let searchQuery = "";
  let selectedDate = "";
  let selectedStatus = "";
  let loading = true;

  // Fetch all forms
  async function fetchForms() {
    try {
      const response = await fetch("http://localhost:8000/api/forms");
      const data = await response.json();
      forms = data;
      loading = false;
    } catch (error) {
      console.error("Error fetching forms:", error);
      loading = false;
    }
  }

  // Delete form
  async function deleteForm(id) {
    if (confirm("Are you sure you want to delete this form?")) {
      try {
        await fetch(`http://localhost:8000/api/forms/${id}`, {
          method: "DELETE",
        });
        forms = forms.filter((form) => form._id !== id);
      } catch (error) {
        console.error("Error deleting form:", error);
      }
    }
  }

  // Search and filter forms
  $: filteredForms = forms.filter((form) => {
    const matchesSearch = form.formName
      .toLowerCase()
      .includes(searchQuery.toLowerCase());
    const matchesDate =
      !selectedDate ||
      new Date(form.createdAt).toLocaleDateString() ===
        new Date(selectedDate).toLocaleDateString();
    const matchesStatus = !selectedStatus || form.status === selectedStatus;
    return matchesSearch && matchesDate && matchesStatus;
  });

  onMount(fetchForms);
</script>

<div class="">
  <Navbar />
  <div class="p-6 max-w-7xl mx-auto">
  
    <!-- Top Matrix Section -->
    <div class="p-6 mb-6">
      <h2 class="text-xl font-semibold mb-4">Form Statistics</h2>
      <div class="grid grid-cols-4 gap-4">
        <div class="bg-white overflow-hidden shadow rounded-lg">
          <div class="p-5">
            <div class="flex items-center">
              <div class="flex-shrink-0">
                <FormInput class="h-6 w-6 text-gray-400" />
              </div>
              <div class="ml-5 w-0 flex-1">
                <dl>
                  <dt class="text-sm font-medium text-gray-500 truncate">
                    Total Templates
                  </dt>
                  <dd class="flex items-baseline">
                    <div class="text-2xl font-semibold text-gray-900">
                      {forms.length}
                    </div>
                  </dd>
                </dl>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  
    <!-- Search Section -->
    <div class="bg-white rounded-lg shadow p-6 mb-6">
      <div class="grid grid-cols-4 gap-4 items-end">
        <div>
          <label class="block text-sm font-medium text-gray-700 mb-1"
            >Search</label
          >
          <input
            type="text"
            bind:value={searchQuery}
            placeholder="ENTER HERE"
            class="w-full px-3 py-2 border border-gray-300 rounded-md"
          />
        </div>
        <div>
          <label class="block text-sm font-medium text-gray-700 mb-1">Date</label>
          <input
            type="date"
            bind:value={selectedDate}
            class="w-full px-3 py-2 border border-gray-300 rounded-md"
          />
        </div>
        <div>
          <label class="block text-sm font-medium text-gray-700 mb-1"
            >Status</label
          >
          <select
            bind:value={selectedStatus}
            class="w-full px-3 py-2 border border-gray-300 rounded-md"
          >
            <option value="">All</option>
            <option value="active">Active</option>
            <option value="inactive">Inactive</option>
          </select>
        </div>
        <button
          class="bg-indigo-600 text-white px-6 py-2 rounded-md hover:bg-indigo-700"
        >
          Submit
        </button>
      </div>
    </div>
  
    <!-- Template Management List -->
    <div class="bg-white rounded-lg shadow">
      <div class="p-6 flex justify-between items-center">
        <h2 class="text-xl font-semibold">Template Management List</h2>
        <div class="flex gap-3">
          <Link
            to="/form-builder"
            class="bg-indigo-600 text-white px-4 py-2 rounded-md hover:bg-indigo-700 flex items-center gap-2"
          >
            <span class="text-xl">+</span> Create a Template
          </Link>
        </div>
      </div>
  
      <!-- Table -->
      <div class="overflow-x-auto">
        <table class="w-full">
          <thead class="bg-gray-100">
            <tr>
              <th class="px-6 py-3 text-left text-sm font-medium text-gray-700"
                >PROCESSID</th
              >
              <th class="px-6 py-3 text-left text-sm font-medium text-gray-700"
                >TEMPLATE NAME</th
              >
              <th class="px-6 py-3 text-left text-sm font-medium text-gray-700"
                >DATE</th
              >
              <th class="px-6 py-3 text-left text-sm font-medium text-gray-700"
                >STATUS</th
              >
              <th class="px-6 py-3 text-left text-sm font-medium text-gray-700"
                >ACTION</th
              >
            </tr>
          </thead>
          <tbody class="divide-y divide-gray-200">
            {#each filteredForms as form, i}
              <tr>
                <td class="px-6 py-4 text-sm text-gray-700">{i + 1}</td>
                <td class="px-6 py-4 text-sm text-gray-700">{form.formName}</td>
                <td class="px-6 py-4 text-sm text-gray-700">
                  {new Date(form.createdAt).toLocaleString()}
                </td>
                <td class="px-6 py-4">
                  <span class="px-2 py-1 text-sm text-green-600">
                    {form.status}
                  </span>
                </td>
                <td class="px-6 py-4">
                  <div class="flex gap-2">
                    <a
                      href={`/edit-form/${form._id}`}
                      class="bg-indigo-600 text-white px-3 py-1 rounded text-sm hover:bg-indigo-700"
                    >
                      View/Edit
                    </a>
                    <button
                      on:click={() => deleteForm(form._id)}
                      class="bg-red-500 text-white px-3 py-1 rounded text-sm hover:bg-red-600"
                    >
                      Delete
                    </button>
                  </div>
                </td>
              </tr>
            {/each}
          </tbody>
        </table>
      </div>
    </div>
  </div>
</div>
