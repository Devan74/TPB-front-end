<script>
  import { createEventDispatcher } from "svelte";
  import axios from "axios";

  let name = "";
  let position = "";
  let department = "";
  let salary = "";

  const dispatch = createEventDispatcher();

  const handleSubmit = async () => {
    try {
      const newEmployee = {
        name,
        position,
        department,
        salary,
      };

      await axios.post("http://localhost:8000/api/employees", newEmployee);
      dispatch("fetchEmployees"); // Notify parent to refresh list
      dispatch("closeModal"); // Notify parent to close the modal
    } catch (err) {
      console.error("Error adding employee:", err);
    }
  };
</script>

<div class="p-6 space-y-4">
  <h2 class="text-xl font-bold">Add Employee</h2>

  <form on:submit|preventDefault={handleSubmit}>
    <!-- Input fields for employee details -->
    <div class="space-y-2">
      <label for="name" class="block">Name</label>
      <input
        id="name"
        type="text"
        bind:value={name}
        class="w-full p-2 border border-gray-300 rounded"
        placeholder="Enter employee name"
        required
      />
    </div>

    <div class="space-y-2">
      <label for="position" class="block">Position</label>
      <input
        id="position"
        type="text"
        bind:value={position}
        class="w-full p-2 border border-gray-300 rounded"
        placeholder="Enter position"
        required
      />
    </div>

    <div class="space-y-2">
      <label for="department" class="block">Department</label>
      <input
        id="department"
        type="text"
        bind:value={department}
        class="w-full p-2 border border-gray-300 rounded"
        placeholder="Enter department"
        required
      />
    </div>

    <div class="space-y-2">
      <label for="salary" class="block">Salary</label>
      <input
        id="salary"
        type="number"
        bind:value={salary}
        class="w-full p-2 border border-gray-300 rounded"
        placeholder="Enter salary"
        required
      />
    </div>

    <div class="mt-4">
      <button
        type="submit"
        class="bg-indigo-600 text-white py-2 px-4 rounded hover:bg-indigo-700"
      >
        Add Employee
      </button>
    </div>
  </form>
</div>
