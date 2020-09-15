<script>
  import { Router, Route } from "svelte-routing";
  import Home from "./routes/Home.svelte";
  import Bucket from "./routes/Bucket.svelte";
  import Login from "./routes/Login.svelte";
  import CreateAccount from "./routes/CreateAccount.svelte";

  export let url = "";
</script>

<!-- App.svelte -->
<Router {url}>
  <Route path="/bucket/:name" component={Bucket} />
  <Route path="/" component={Login} />
  <Route path="/home" component={Home} />
  <Route path="/create-account" component={CreateAccount} /> 
</Router>
