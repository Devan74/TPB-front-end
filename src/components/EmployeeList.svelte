<script>
  import axios from "axios";
  import { Plus } from "lucide-svelte";
  import EmployeeForm from "./EmployeeForm.svelte";

  let employees = [];
  let showModal = false;

  const fetchEmployees = async () => {
    try {
      const response = await axios.get("http://localhost:8000/api/employees");
      employees = response.data;
    } catch (err) {
      console.error("Error fetching employees:", err);
    }
  };

  let isLoading = false;

  const deleteEmployee = async (id) => {
    isLoading = true;
    try {
      const response = await axios.delete(
        `http://localhost:8000/api/employees/${id}`
      );
      if (response.status === 200) {
        alert(response.data.message || "Employee deleted successfully");
        await fetchEmployees();
      }
    } catch (err) {
      console.error("Error deleting employee:", err);
      alert(err.response?.data?.message || "Failed to delete employee");
    } finally {
      isLoading = false;
    }
  };

  const openModal = () => {
    showModal = true;
  };

  const closeModal = () => {
    showModal = false;
  };

  fetchEmployees();
</script>

<div class="max-w-4xl mx-auto p-6">
  <div class="flex justify-between items-center mb-4">
    <h1 class="text-xl font-bold">Employee List</h1>
    <button
      class="flex items-center bg-indigo-600 text-white py-2 px-4 rounded-lg hover:bg-indigo-700"
      on:click={openModal}
    >
      <Plus class="w-5 h-5 mr-2" />
      Add Employee
    </button>
  </div>

  <table class="min-w-full bg-white border border-gray-200">
    <thead>
      <tr>
        <th class="p-2 border-b">Name</th>
        <th class="p-2 border-b">Position</th>
        <th class="p-2 border-b">Department</th>
        <th class="p-2 border-b">Salary</th>
        <th class="p-2 border-b">Actions</th>
      </tr>
    </thead>
    <tbody>
      {#each employees as employee}
        <tr class="text-center">
          <td class="p-2 border-b">{employee.name}</td>
          <td class="p-2 border-b">{employee.position}</td>
          <td class="p-2 border-b">{employee.department}</td>
          <td class="p-2 border-b">{employee.salary}</td>
          <td class="p-2 border-b">
            <button
              on:click={() => deleteEmployee(employee._id)}
              class="bg-red-500 text-white p-2 rounded-md"
              disabled={isLoading}
            >
              {isLoading ? "Deleting..." : "Delete"}
            </button>
          </td>
        </tr>
      {/each}
    </tbody>
  </table>

  {#if showModal}
    <div
      class="fixed inset-0 bg-black bg-opacity-50 flex justify-center items-center z-50"
    >
      <div class="bg-white p-6 rounded-lg w-96">
        <EmployeeForm
          on:fetchEmployees={fetchEmployees}
          on:closeModal={closeModal}
        />
        <div class="flex justify-end mt-4">
          <button
            on:click={closeModal}
            class="bg-gray-500 text-white py-2 px-4 rounded hover:bg-gray-600"
          >
            Close
          </button>
        </div>
      </div>
    </div>
  {/if}
</div>
