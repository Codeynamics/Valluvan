<script lang="ts">
  import data from '../data/thirukkural.json';

  type Kural = {
    number: number;
    line1: string;
    line2: string;
    tamilExplanation: string;
    englishTranslation: string;
  };

  type Adhikaram = {
    number: number;
    name: string;
    translation: string;
    transliteration: string;
    kurals: Kural[];
  };

  type Iyal = {
    name: string;
    transliteration: string;
    translation: string;
    adhikarams: Adhikaram[];
  };

  type Paal = {
    name: string;
    transliteration: string;
    translation: string;
    iyals: Iyal[];
  };

  const paals: Paal[] = data.paals;

  let openPaal = $state<number | null>(0);
  let openIyal = $state<string | null>(null);
  let openAdhikaram = $state<number | null>(null);
  let searchQuery = $state('');

  const paalStyles = [
    {
      accent: 'border-l-gold',
      badge: 'bg-gold/10 text-gold border border-gold/30',
      numBg: 'bg-gold',
    },
    {
      accent: 'border-l-gold',
      badge: 'bg-gold/10 text-gold border border-gold/30',
      numBg: 'bg-gold',
    },
    {
      accent: 'border-l-gold',
      badge: 'bg-gold/10 text-gold border border-gold/30',
      numBg: 'bg-gold',
    },
  ];

  function togglePaal(idx: number) {
    openPaal = openPaal === idx ? null : idx;
    openIyal = null;
    openAdhikaram = null;
  }

  function toggleIyal(key: string) {
    openIyal = openIyal === key ? null : key;
    openAdhikaram = null;
  }

  function toggleAdhikaram(num: number) {
    openAdhikaram = openAdhikaram === num ? null : num;
  }

  function iyalKey(paalIdx: number, iyalIdx: number): string {
    return `${paalIdx}-${iyalIdx}`;
  }

  let filteredPaals = $derived.by(() => {
    if (!searchQuery.trim()) return paals;
    const q = searchQuery.toLowerCase().trim();
    return paals
      .map((paal) => {
        const filteredIyals = paal.iyals
          .map((iyal) => {
            const filteredAdhikarams = iyal.adhikarams.filter(
              (a) =>
                a.name.toLowerCase().includes(q) ||
                a.translation.toLowerCase().includes(q) ||
                a.transliteration.toLowerCase().includes(q) ||
                a.kurals.some(
                  (k) =>
                    k.line1.toLowerCase().includes(q) ||
                    k.line2.toLowerCase().includes(q) ||
                    k.englishTranslation.toLowerCase().includes(q) ||
                    k.tamilExplanation.toLowerCase().includes(q) ||
                    k.number.toString() === q
                )
            );
            return { ...iyal, adhikarams: filteredAdhikarams };
          })
          .filter((iyal) => iyal.adhikarams.length > 0);
        return { ...paal, iyals: filteredIyals };
      })
      .filter((paal) => paal.iyals.length > 0);
  });

  let totalResults = $derived.by(() => {
    return filteredPaals.reduce(
      (sum, paal) =>
        sum +
        paal.iyals.reduce(
          (s, iyal) => s + iyal.adhikarams.reduce((a, ad) => a + ad.kurals.length, 0),
          0
        ),
      0
    );
  });
</script>

<svelte:head>
  <title>Thirukkural — Valluvan</title>
</svelte:head>

<section class="min-h-screen bg-black px-4 py-20 sm:px-6">
  <div class="mx-auto max-w-4xl">
    <div class="text-center">
      <p class="text-xs font-bold uppercase tracking-[0.2em] text-gold">திருக்குறள்</p>
      <h1 class="mt-3 text-3xl font-bold text-white md:text-4xl">Thirukkural</h1>
      <p class="mx-auto mt-3 max-w-lg text-sm leading-relaxed text-white/50">
        All 1330 couplets by Thiruvalluvar, organized by Paal, Iyal, and Adhikaram
      </p>
    </div>

    <div class="mt-8">
      <div class="relative">
        <svg
          class="absolute left-4 top-1/2 h-5 w-5 -translate-y-1/2 text-white/40"
          xmlns="http://www.w3.org/2000/svg"
          fill="none"
          viewBox="0 0 24 24"
          stroke="currentColor"
          stroke-width="2"
        >
          <path
            stroke-linecap="round"
            stroke-linejoin="round"
            d="M21 21l-5.197-5.197m0 0A7.5 7.5 0 105.196 5.196a7.5 7.5 0 0010.607 10.607z"
          />
        </svg>
        <input
          type="text"
          bind:value={searchQuery}
          placeholder="Search kurals, chapters, translations..."
          class="w-full rounded-xl border border-silver/20 bg-surface py-3.5 pl-12 pr-4 text-sm text-white shadow-sm outline-none transition-colors placeholder:text-white/40 focus:border-gold focus:ring-2 focus:ring-gold/20"
        />
      </div>
      {#if searchQuery.trim()}
        <p class="mt-2 text-xs text-white/40">
          {totalResults} kural{totalResults !== 1 ? 's' : ''} found
        </p>
      {/if}
    </div>

    <div class="mt-8 space-y-4">
      {#each filteredPaals as paal, paalIdx (paalIdx)}
        {@const style = paalStyles[paalIdx] || paalStyles[0]}
        <div class="overflow-hidden rounded-2xl border border-silver/15 bg-surface shadow-sm">
          <button
            onclick={() => togglePaal(paalIdx)}
            class="flex w-full items-center justify-between border-l-4 {style.accent} px-6 py-5 text-left transition-colors hover:bg-surface-2"
          >
            <div>
              <div class="flex items-center gap-3">
                <h2 class="text-xl font-bold text-white font-noto">{paal.name}</h2>
                <span class="rounded-full px-2.5 py-0.5 text-xs font-semibold {style.badge}"
                  >{paal.translation}</span
                >
              </div>
              <p class="mt-1 text-xs text-white/40 font-noto">
                {paal.transliteration} &middot; {paal.iyals.reduce(
                  (s, i) => s + i.adhikarams.length,
                  0
                )} adhikarams &middot; {paal.iyals.reduce(
                  (s, i) => s + i.adhikarams.reduce((a, ad) => a + ad.kurals.length, 0),
                  0
                )} kurals
              </p>
            </div>
            <svg
              class="h-5 w-5 shrink-0 text-white/40 transition-transform duration-200 {openPaal ===
              paalIdx
                ? 'rotate-180'
                : ''}"
              xmlns="http://www.w3.org/2000/svg"
              fill="none"
              viewBox="0 0 24 24"
              stroke="currentColor"
              stroke-width="2"
            >
              <path stroke-linecap="round" stroke-linejoin="round" d="M19 9l-7 7-7-7" />
            </svg>
          </button>

          {#if openPaal === paalIdx}
            <div class="border-t border-silver/15">
              {#each paal.iyals as iyal, iyalIdx (iyalIdx)}
                {@const key = iyalKey(paalIdx, iyalIdx)}
                <div class={iyalIdx > 0 ? 'border-t border-silver/15' : ''}>
                  <button
                    onclick={() => toggleIyal(key)}
                    class="flex w-full items-center justify-between px-6 py-4 text-left transition-colors hover:bg-surface-2"
                  >
                    <div class="flex items-center gap-2">
                      <span class="h-1.5 w-1.5 rounded-full bg-silver/50"></span>
                      <span class="text-sm font-semibold text-white/90 font-noto">{iyal.name}</span>
                      <span class="text-xs text-white/40">({iyal.translation})</span>
                    </div>
                    <svg
                      class="h-4 w-4 text-white/40 transition-transform duration-200 {openIyal ===
                      key
                        ? 'rotate-180'
                        : ''}"
                      xmlns="http://www.w3.org/2000/svg"
                      fill="none"
                      viewBox="0 0 24 24"
                      stroke="currentColor"
                      stroke-width="2"
                    >
                      <path stroke-linecap="round" stroke-linejoin="round" d="M19 9l-7 7-7-7" />
                    </svg>
                  </button>

                  {#if openIyal === key}
                    <div class="border-t border-silver/10 bg-black/40">
                      {#each iyal.adhikarams as adhikaram, adIdx (adhikaram.number)}
                        <div class={adIdx > 0 ? 'border-t border-silver/15' : ''}>
                          <button
                            onclick={() => toggleAdhikaram(adhikaram.number)}
                            class="flex w-full items-center justify-between px-6 py-3.5 text-left transition-colors hover:bg-surface-2"
                          >
                            <div class="flex items-center gap-3">
                              <span
                                class="flex h-7 w-7 shrink-0 items-center justify-center rounded-lg {style.numBg} text-[10px] font-bold text-black"
                              >
                                {adhikaram.number}
                              </span>
                              <div class="text-left">
                                <span class="text-sm font-semibold text-white/90 font-noto"
                                  >{adhikaram.name}</span
                                >
                                <span class="ml-2 text-xs text-white/40"
                                  >{adhikaram.translation}</span
                                >
                              </div>
                            </div>
                            <svg
                              class="h-4 w-4 text-white/40 transition-transform duration-200 {openAdhikaram ===
                              adhikaram.number
                                ? 'rotate-180'
                                : ''}"
                              xmlns="http://www.w3.org/2000/svg"
                              fill="none"
                              viewBox="0 0 24 24"
                              stroke="currentColor"
                              stroke-width="2"
                            >
                              <path
                                stroke-linecap="round"
                                stroke-linejoin="round"
                                d="M19 9l-7 7-7-7"
                              />
                            </svg>
                          </button>

                          {#if openAdhikaram === adhikaram.number}
                            <div class="border-t border-silver/15 bg-surface px-6 py-4">
                              <div class="space-y-4">
                                {#each adhikaram.kurals as kural (kural.number)}
                                  <div class="rounded-xl border border-silver/15 bg-black/40 p-5">
                                    <div class="mb-3 flex items-center gap-2">
                                      <span
                                        class="rounded-md bg-gold/15 px-2 py-0.5 text-[10px] font-bold text-gold"
                                      >
                                        #{kural.number}
                                      </span>
                                    </div>
                                    <p class="font-noto text-base leading-relaxed text-white">
                                      {kural.line1}
                                    </p>
                                    <p class="font-noto text-base leading-relaxed text-white">
                                      {kural.line2}
                                    </p>
                                    <div class="mt-3 space-y-2 border-t border-silver/15 pt-3">
                                      <p class="font-noto text-sm leading-relaxed text-white/60">
                                        {kural.tamilExplanation}
                                      </p>
                                      <p class="text-sm leading-relaxed text-white/50 italic">
                                        {kural.englishTranslation}
                                      </p>
                                    </div>
                                  </div>
                                {/each}
                              </div>
                            </div>
                          {/if}
                        </div>
                      {/each}
                    </div>
                  {/if}
                </div>
              {/each}
            </div>
          {/if}
        </div>
      {/each}
    </div>

    {#if filteredPaals.length === 0}
      <div class="mt-12 text-center">
        <p class="text-white/40 text-sm">No kurals found for "{searchQuery}"</p>
      </div>
    {/if}
  </div>
</section>
