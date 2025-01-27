<script>
  import { LogOut } from "lucide-svelte";
  import { Link, navigate } from "svelte-routing";

  const logout = () => {
    localStorage.removeItem("token");
    navigate("/login");
  };
</script>

<header class="bg-white border-b sticky top-0 z-10">
  <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
    <div class="flex justify-between items-center h-16">
      <div class="flex items-center">
        <nav class="ml-10 space-x-4">
          <!-- <Link
            to="/order-management"
            class="text-gray-500 px-3 py-2 font-medium">Order Management</Link
          > -->
          <Link
            to="/template-management"
            class="text-gray-500 hover:text-indigo-500 px-3 py-2 font-medium"
            >Template Management</Link
          >
          <!-- <Link to="#" class="text-gray-500 px-3 py-2 font-medium"
            >Document Type</Link
          > -->
        </nav>
      </div>

      <div class="flex items-center space-x-4" title="Logout">
        <span class="text-gray-700">Fidelity National Financial</span>
        <button on:click={logout} class="text-gray-400 hover:text-gray-500">
          <LogOut class="h-6 w-6" />
        </button>
      </div>
    </div>
  </div>
</header>
