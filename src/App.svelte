<script>
  import { Router, Route, navigate } from "svelte-routing";
  import FormBuilder from "./components/FormBuilder.svelte";
  import FormTemp from "./components/FormTemp.svelte";
  import Login from "./pages/Login.svelte";
  import TemplateManagement from "./pages/TemplateManagement.svelte";

  const token = localStorage.getItem("token");
  if (!token) {
    navigate("/login");
  }
</script>

<Router>
  <main class="min-h-screen">
    <Route path="/login" let:params>
      <Login />
    </Route>
    <Route path="/template-management" let:params>
      <TemplateManagement />
    </Route>
    <Route path="/form-builder" let:params>
      <FormBuilder />
    </Route>
    <Route path="/form-template" let:params>
      <FormTemp />
    </Route>
  </main>
</Router>
