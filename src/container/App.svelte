<script>
  import { Router, Link, Route } from "svelte-routing";
  import Home from "./routes/Home.svelte";
  import Bucket from "./routes/Bucket.svelte";

  export let url = "";
</script>

<!-- App.svelte -->
<Router {url}>
  <Route path="bucket/:name" component={Bucket} />
  <Route path="/" component={Home} />
</Router>
