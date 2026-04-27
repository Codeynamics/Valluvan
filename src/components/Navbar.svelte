<script lang="ts">
  import { link, push } from 'svelte-spa-router';
  let menuOpen = $state(false);
  const links = [
    { name: 'Features', id: 'features' },
    { name: 'Competition', id: 'competition' },
    { name: 'Prizes', id: 'prizes' },
    { name: 'Contact', id: 'contact' },
  ];

  function scrollTo(id: string) {
    return (e: Event) => {
      e.preventDefault();
      menuOpen = false;
      push('/');
      setTimeout(() => {
        document.getElementById(id)?.scrollIntoView({ behavior: 'smooth' });
      }, 50);
    };
  }
</script>

<nav class="sticky top-0 z-50 w-full border-b border-red-100 bg-white/90 backdrop-blur">
  <div class="mx-auto flex max-w-7xl items-center justify-between px-6 py-4">
    <a
      href="/"
      use:link
      class="flex items-center gap-2 text-2xl font-bold text-red-600"
    >
      <svg class="h-8 w-8" viewBox="0 0 40 40" fill="none" xmlns="http://www.w3.org/2000/svg">
        <circle cx="20" cy="20" r="18" stroke="currentColor" stroke-width="3"/>
        <path d="M12 28L20 12L28 28" stroke="currentColor" stroke-width="3" stroke-linecap="round" stroke-linejoin="round"/>
        <circle cx="20" cy="22" r="3" fill="currentColor"/>
      </svg>
      <span class="font-noto">வள்ளுவன்</span>
    </a>

    <div class="hidden items-center gap-8 md:flex">
      {#each links as l (l.name)}
        <a href="/#{l.id}" onclick={scrollTo(l.id)} class="text-sm font-medium text-gray-600 transition-colors hover:text-red-600">
          {l.name}
        </a>
      {/each}
      <a href="/" use:link class="rounded-full bg-red-600 px-5 py-2.5 text-sm font-semibold text-white transition-colors hover:bg-red-700">
        Get Started
      </a>
    </div>

    <button
      class="md:hidden"
      onclick={() => menuOpen = !menuOpen}
      aria-label="Toggle menu"
    >
      <svg class="h-6 w-6 text-gray-700" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24">
        {#if menuOpen}
          <path stroke-linecap="round" stroke-linejoin="round" d="M6 18L18 6M6 6l12 12"/>
        {:else}
          <path stroke-linecap="round" stroke-linejoin="round" d="M4 6h16M4 12h16M4 18h16"/>
        {/if}
      </svg>
    </button>
  </div>

  {#if menuOpen}
    <div class="border-t border-red-100 bg-white px-6 py-4 md:hidden">
      <div class="flex flex-col gap-4">
        {#each links as l (l.name)}
          <a href="/#{l.id}" class="text-sm font-medium text-gray-600 hover:text-red-600" onclick={scrollTo(l.id)}>
            {l.name}
          </a>
        {/each}
        <a href="/" use:link class="rounded-full bg-red-600 px-5 py-2.5 text-center text-sm font-semibold text-white hover:bg-red-700">
          Get Started
        </a>
      </div>
    </div>
  {/if}
</nav>