<script>
  import Router from "svelte-spa-router";
  import Home from "./routes/Home.svelte";
  import Conjugator from "./routes/Conjugator.svelte";
  import Keyboard from "./routes/Keyboard.svelte";
  import Participate from "./routes/Participate.svelte";
  import NotFound from "./routes/NotFound.svelte";
  import Navbar from "./components/Navbar/Navbar.svelte";

  const routes = {
    "/": Home,
    "/conjugator": Conjugator,
    "/keyboard": Keyboard,
    "/participate": Participate,
    "*": NotFound
  };
</script>

<Navbar />
<Router {routes} />
