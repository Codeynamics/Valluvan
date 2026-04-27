<script>
  import { link } from 'svelte-spa-router';
  const features = [
    {
      title: 'Dashboard',
      tamil: 'முகப்பு',
      desc: 'Track your progress, daily streaks, and overall ranking in a clean, intuitive interface.',
    },
    {
      title: 'History',
      tamil: 'வரலாறு',
      desc: 'Review every Kural you have studied. Filter by adhikaram, search keywords, and revisit favorites.',
    },
    {
      title: 'Competition',
      tamil: 'போட்டி',
      desc: 'Challenge friends or join global leaderboards. Weekly quizzes and timed Kural identification games.',
    },
    {
      title: 'Random Thirukural',
      tamil: 'தற்செயல் குறள்',
      desc: 'Discover a new Kural on every visit. Let ancient wisdom find you through serendipity.',
      link: '/random',
    },
    {
      title: 'Prizes',
      tamil: 'பரிசுகள்',
      desc: 'Earn badges, certificates, and real rewards for top performers in every competition cycle.',
    },
    {
      title: 'Winners',
      tamil: 'வெற்றியாளர்',
      desc: 'Celebrate top learners on the Hall of Fame. See monthly and all-time rankings with stats.',
    },
  ];
</script>

<section id="features" class="bg-white px-6 py-20 md:py-28">
  <div class="mx-auto max-w-7xl">
    <div class="text-center">
      <p class="text-sm font-semibold uppercase tracking-wider text-red-600">Features</p>
      <h2 class="mt-2 text-3xl font-bold text-gray-900 md:text-4xl">Everything to master Thirukkural</h2>
      <p class="mx-auto mt-4 max-w-2xl text-gray-600">A complete toolkit for learners of all ages. From daily study to global competitions.</p>
    </div>

    <div class="mt-14 grid gap-8 sm:grid-cols-2 lg:grid-cols-3">
      {#each features as f (f.title)}
        <div class="relative group rounded-2xl border border-gray-100 bg-white p-8 shadow-sm transition-shadow hover:shadow-md {f.link ? 'cursor-pointer' : ''}">
          {#if f.link}
            <a href={f.link} use:link aria-label={f.title} class="absolute inset-0 z-10"></a>
          {/if}
          <div class="inline-flex h-12 w-12 items-center justify-center rounded-xl bg-red-100 text-red-600">
            {#if f.title === 'Dashboard'}
              <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                <rect x="3" y="3" width="7" height="7" rx="1"/><rect x="14" y="3" width="7" height="7" rx="1"/><rect x="14" y="14" width="7" height="7" rx="1"/><rect x="3" y="14" width="7" height="7" rx="1"/>
              </svg>
            {:else if f.title === 'History'}
              <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                <path stroke-linecap="round" stroke-linejoin="round" d="M12 8v4l3 3m6-3a9 9 0 11-18 0 9 9 0 0118 0z"/>
              </svg>
            {:else if f.title === 'Competition'}
              <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                <path stroke-linecap="round" stroke-linejoin="round" d="M13 10V3L4 14h7v7l9-11h-7z"/>
              </svg>
            {:else if f.title === 'Random Thirukural'}
              <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                <rect x="3" y="3" width="18" height="18" rx="2" ry="2"/>
                <circle cx="8" cy="8" r="1.5" fill="currentColor" stroke="none"/>
                <circle cx="16" cy="8" r="1.5" fill="currentColor" stroke="none"/>
                <circle cx="8" cy="16" r="1.5" fill="currentColor" stroke="none"/>
                <circle cx="16" cy="16" r="1.5" fill="currentColor" stroke="none"/>
                <circle cx="12" cy="12" r="1.5" fill="currentColor" stroke="none"/>
              </svg>
            {:else if f.title === 'Prizes'}
              <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                <path stroke-linecap="round" stroke-linejoin="round" d="M12 8c-1.657 0-3 .895-3 2s1.343 2 3 2 3 .895 3 2-1.343 2-3 2m0-8c1.11 0 2.08.402 2.599 1M12 8V7m0 1v8m0 0v1m0-1c-1.11 0-2.08-.402-2.599-1M21 12a9 9 0 11-18 0 9 9 0 0118 0z"/>
              </svg>
            {:else if f.title === 'Winners'}
              <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                <path stroke-linecap="round" stroke-linejoin="round" d="M5 3v4M3 5h4M6 17v4m-2-2h4m5-16l2.286 6.857L21 12l-5.714 2.143L13 21l-2.286-6.857L5 12l5.714-2.143L13 3z"/>
              </svg>
            {/if}
          </div>
          <h3 class="mt-5 text-lg font-bold text-gray-900">
            {f.title}
            <span class="ml-2 block text-sm font-medium text-red-500 font-noto">{f.tamil}</span>
          </h3>
          <p class="mt-3 text-sm leading-relaxed text-gray-600">{f.desc}</p>
        </div>
      {/each}
    </div>
  </div>
</section>