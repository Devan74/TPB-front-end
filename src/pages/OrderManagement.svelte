<script>
  import {
    Mail,
    Database,
    Cog,
    Check,
  } from "lucide-svelte";
  import Navbar from "../components/Navbar.svelte";

  // Sample data
  const metrics = [
    { icon: Mail, count: 2, label: "Total Emails" },
    { icon: Database, count: 1, label: "Total Queue" },
    { icon: Cog, count: 1, label: "Email Processing" },
    { icon: Check, count: 0, label: "Today's Completion" },
  ];

  const orders = [
    {
      subject:
        "NEW ORDER | 5B-PG | PROPERTY ADDRESS 780 S. Sapodilla Ave., Apt. 112, West Palm Beach, FL 33401 | CLOSING DATE 11/15",
      toEmail:
        "FLOrderEntry <FLOrderEntry@fnf.com>; FL-Trident-Intake <intake@tridenttitlellc.com>",
      fromEmail: "Jennifer.Rinderknecht@tridenttitlellc.com",
      date: "2024-10-16T14:25:40",
      status: "New",
    },
  ];

  let searchQuery = "";
  let selectedDate = "";
  let selectedStatus = "";
</script>

<div class="min-h-screen bg-gray-50">
  <Navbar />  
  <main class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-8">
    <!-- Metrics -->
    <div class="grid grid-cols-1 gap-5 sm:grid-cols-2 lg:grid-cols-4">
      {#each metrics as { icon: Icon, count, label }}
        <div class="bg-white overflow-hidden shadow rounded-lg">
          <div class="p-5">
            <div class="flex items-center">
              <div class="flex-shrink-0">
                <Icon class="h-6 w-6 text-gray-400" />
              </div>
              <div class="ml-5 w-0 flex-1">
                <dl>
                  <dt class="text-sm font-medium text-gray-500 truncate">
                    {label}
                  </dt>
                  <dd class="flex items-baseline">
                    <div class="text-2xl font-semibold text-gray-900">
                      {count}
                    </div>
                  </dd>
                </dl>
              </div>
            </div>
          </div>
        </div>
      {/each}
    </div>

    <!-- Search Form -->
    <div class="mt-8 bg-white shadow rounded-lg p-6">
        <div class="grid grid-cols-4 gap-6">
          <div>
            <label for="search" class="block text-sm font-medium text-gray-700">Search</label>
            <input
              type="text"
              bind:value={searchQuery}
              class="mt-1 block w-full rounded-md border-2 border-gray-300 focus:border-indigo-500 focus:outline-none px-4 py-2 text-gray-700"
              placeholder="ENTER HERE"
            />
          </div>
          <div>
            <label for="date" class="block text-sm font-medium text-gray-700">Date</label>
            <input
              type="date"
              bind:value={selectedDate}
              class="mt-1 block w-full rounded-md border-2 border-gray-300 focus:border-indigo-500 focus:outline-none px-4 py-2 text-gray-700"
            />
          </div>
          <div>
            <label for="status" class="block text-sm font-medium text-gray-700">Status</label>
            <select
              bind:value={selectedStatus}
              class="mt-1 block w-full rounded-md border-2 border-gray-300 focus:border-indigo-500 focus:outline-none px-4 py-2 text-gray-700"
            >
              <option value="">Select Status</option>
              <option value="new">New</option>
              <option value="processing">Processing</option>
              <option value="completed">Completed</option>
            </select>
          </div>
          <div class="">
            <button
              class="inline-flex items-center  px-4 py-2 border mt-5 border-transparent text-sm font-medium rounded-md text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-indigo-500"
            >
              Submit
            </button>
          </div>
        </div>
      </div>
      
    <!-- Order List -->
    <div class="mt-8 bg-white shadow rounded-lg">
      <div class="px-4 py-5 sm:px-6 flex justify-between items-center">
        <h3 class="text-lg leading-6 font-medium text-gray-900">
          Order Management List
        </h3>
        <button
          class="inline-flex items-center px-4 py-2 border border-gray-300 text-sm font-medium rounded-md text-gray-700 bg-white hover:bg-gray-50"
        >
          Export
        </button>
      </div>
      <div class="overflow-x-auto">
        <table class="min-w-full divide-y divide-gray-200">
          <thead class="bg-gray-50">
            <tr>
              <th
                class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider"
                >Subject</th
              >
              <th
                class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider"
                >To Email</th
              >
              <th
                class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider"
                >From Email</th
              >
              <th
                class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider"
                >Date</th
              >
              <th
                class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider"
                >Status</th
              >
              <th
                class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider"
                >Action</th
              >
            </tr>
          </thead>
          <tbody class="bg-white divide-y divide-gray-200">
            {#each orders as order}
              <tr>
                <td class="px-6 py-4 whitespace-normal text-sm text-gray-900"
                  >{order.subject}</td
                >
                <td class="px-6 py-4 whitespace-normal text-sm text-gray-500"
                  >{order.toEmail}</td
                >
                <td class="px-6 py-4 whitespace-normal text-sm text-gray-500"
                  >{order.fromEmail}</td
                >
                <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500"
                  >{new Date(order.date).toLocaleString()}</td
                >
                <td class="px-6 py-4 whitespace-nowrap">
                  <span
                    class="px-2 inline-flex text-xs leading-5 font-semibold rounded-full bg-green-100 text-green-800"
                  >
                    {order.status}
                  </span>
                </td>
                <td class="px-6 py-4 whitespace-nowrap text-sm font-medium">
                    <button
                      class="text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:ring-offset-2 rounded-md px-4 py-2 mr-4 transition-all duration-300"
                    >
                      View/Edit
                    </button>
                    <button
                      class="text-white bg-red-500 hover:bg-red-600 focus:outline-none focus:ring-2 focus:ring-red-500 focus:ring-offset-2 rounded-md px-4 py-2 transition-all duration-300"
                    >
                      Delete
                    </button>
                  </td>                  
              </tr>
            {/each}
          </tbody>
        </table>
      </div>
    </div>
  </main>
</div>

<style>
  :global(body) {
    margin: 0;
    font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto,
      "Helvetica Neue", Arial, sans-serif;
  }
</style>
