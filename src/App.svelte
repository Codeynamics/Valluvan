<script>
  import Router from 'svelte-spa-router';
  import Navbar from './components/Navbar.svelte';
  import Home from './pages/Home.svelte';
  import Random from './pages/Random.svelte';

  const routes = {
    '/': Home,
    '/random': Random,
  };
</script>

<Navbar />
<Router {routes} />