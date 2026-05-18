<script>
  import Router from 'svelte-spa-router';
  import Navbar from './components/Navbar.svelte';
  import Home from './pages/Home.svelte';
  import Random from './pages/Random.svelte';
  import { wrap } from 'svelte-spa-router/wrap';

  const routes = {
    '/': Home,
    '/random': Random,
    '/kural': wrap({
      asyncComponent: () => import('./pages/Kural.svelte'),
    }),
  };
</script>

<Navbar />
<Router {routes} />