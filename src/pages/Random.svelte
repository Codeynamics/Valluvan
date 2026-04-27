<script lang="ts">
  let isSpinning = $state(false);
  let finalNumber = $state<number | null>(null);
  let displayedNumber = $state<number | null>(null);

  let coinEl: HTMLDivElement;
  let reelEls = $state<HTMLDivElement[]>([]);

  const DIGIT_HEIGHT = 96;
  const REEL_CYCLE = DIGIT_HEIGHT * 10;

  function getRandomKural(): number {
    return Math.floor(Math.random() * 1330) + 1;
  }

  function pad4(n: number): string {
    return n.toString().padStart(4, '0');
  }

  function getFinalCoinRotation(targetDigit: number, currentAngle: number): number {
    const base = targetDigit === 0 ? 0 : 180;
    const minTarget = currentAngle + 360;
    const k = Math.ceil((minTarget - base) / 360);
    return base + k * 360;
  }

  function getFinalReelOffset(targetDigit: number, currentOffset: number): number {
    const digitOffset = targetDigit * DIGIT_HEIGHT;
    const extraCycles = 4 + Math.floor(Math.random() * 4);
    const target = currentOffset + extraCycles * REEL_CYCLE + digitOffset;
    const aligned = Math.floor(target / REEL_CYCLE) * REEL_CYCLE + digitOffset;
    return aligned;
  }

  async function generate() {
    if (isSpinning) return;

    const number = getRandomKural();
    const digits = pad4(number);
    finalNumber = number;
    displayedNumber = null;
    isSpinning = true;

    await new Promise((r) => setTimeout(r, 1500));

    if (coinEl) {
      const computed = getComputedStyle(coinEl);
      const matrix = new DOMMatrix(computed.transform);
      const angle = Math.atan2(matrix.m13, matrix.m11) * (180 / Math.PI);
      const finalAngle = getFinalCoinRotation(parseInt(digits[0]), angle);
      coinEl.style.transform = `rotateY(${angle}deg)`;
      coinEl.classList.remove('spinning');
      requestAnimationFrame(() => {
        coinEl.style.transition = 'transform 0.8s cubic-bezier(0.23, 1, 0.32, 1)';
        coinEl.style.transform = `rotateY(${finalAngle}deg)`;
      });
    }

    reelEls.forEach((el, i) => {
      if (!el) return;
      const computed = getComputedStyle(el);
      const matrix = new DOMMatrix(computed.transform);
      const currentOffset = -matrix.m42;
      const targetDigit = parseInt(digits[i + 1]);
      const finalOffset = getFinalReelOffset(targetDigit, currentOffset);
      el.style.transform = `translateY(-${currentOffset}px)`;
      el.classList.remove('spinning');
      requestAnimationFrame(() => {
        el.style.transition = 'transform 0.8s cubic-bezier(0.23, 1, 0.32, 1)';
        el.style.transform = `translateY(-${finalOffset}px)`;
      });
    });

    await new Promise((r) => setTimeout(r, 500));
    displayedNumber = finalNumber;
    isSpinning = false;
  }

  function resetStyles() {
    if (coinEl) {
      coinEl.style.transition = '';
      coinEl.style.transform = '';
    }
    reelEls.forEach((el) => {
      if (!el) return;
      el.style.transition = '';
      el.style.transform = '';
    });
  }

  $effect(() => {
    if (isSpinning) {
      resetStyles();
    }
  });
</script>

<svelte:head>
  <title>Random Thirukural — Valluvan</title>
</svelte:head>

<section class="flex min-h-screen flex-col items-center bg-gray-50 px-6 py-24">
  <!-- Header -->
  <div class="text-center">
    <p class="text-xs font-bold uppercase tracking-[0.2em] text-red-600">தற்செயல் குறள்</p>
    <h1 class="mt-3 text-3xl font-bold text-gray-900 md:text-4xl">Random Thirukural</h1>
    <p class="mx-auto mt-3 max-w-md text-sm leading-relaxed text-gray-500">Let fate choose a Kural for you. Spin the reels and discover ancient wisdom.</p>
  </div>

  <!-- Slot Machine -->
  <div class="mt-14">
    <div class="relative rounded-3xl border border-gray-200 bg-white p-4 shadow-xl shadow-gray-200/60 md:p-6">
      <!-- Top accent line -->
      <div class="absolute inset-x-0 top-0 h-1 rounded-t-3xl bg-red-600"></div>

      <!-- Reels container -->
      <div class="flex items-center gap-3 md:gap-4">
        <!-- Coin (first digit) -->
        <div class="coin-scene">
          <div bind:this={coinEl} class="coin" class:spinning={isSpinning}>
            <div class="coin-face coin-front">0</div>
            <div class="coin-face coin-back">1</div>
          </div>
        </div>

        <!-- Divider -->
        <div class="h-20 w-px bg-gray-200 md:h-24"></div>

        <!-- Vertical spinners (digits 2-4) -->
        {#each [0, 1, 2] as idx (idx)}
          <div class="reel-window">
            <div bind:this={reelEls[idx]} class="reel" class:spinning={isSpinning}>
              {#each Array.from({ length: 10 }) as _, cycle}
                {#each [0, 1, 2, 3, 4, 5, 6, 7, 8, 9] as d}
                  <div class="reel-digit">{d}</div>
                {/each}
              {/each}
            </div>
          </div>
        {/each}
      </div>
    </div>
  </div>

  <!-- Button -->
  <button
    onclick={generate}
    disabled={isSpinning}
    class="mt-10 inline-flex w-64 items-center justify-center rounded-full bg-red-600 px-6 py-3.5 text-sm font-semibold text-white shadow-lg shadow-red-200/80 transition-all hover:bg-red-700 hover:shadow-xl hover:shadow-red-200 active:scale-[0.97] disabled:cursor-not-allowed disabled:opacity-60"
  >
    {#if isSpinning}
      <svg
        class="mr-2 h-4 w-4 animate-spin"
        xmlns="http://www.w3.org/2000/svg"
        fill="none"
        viewBox="0 0 24 24"
      >
        <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"
        ></circle>
        <path
          class="opacity-75"
          fill="currentColor"
          d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4z"
        ></path>
      </svg>
      <span class="w-24 text-center">Spinning...</span>
    {:else}
      <span class="w-24 text-center">Generate</span>
    {/if}
  </button>

  <!-- Result -->
  {#if displayedNumber !== null}
    <div class="mt-12 w-full max-w-xs">
      <div class="rounded-2xl border border-gray-100 bg-white px-8 py-7 shadow-lg shadow-gray-200/50 text-center">
        <p class="text-[10px] font-bold uppercase tracking-[0.2em] text-gray-400">Kural Number</p>
        <p class="mt-2 text-5xl font-bold text-red-600 md:text-6xl">{displayedNumber}</p>
        <p class="mt-2 font-mono text-xs text-gray-300">#{pad4(displayedNumber)}</p>
      </div>
    </div>
  {/if}
</section>

<style>
  .coin-scene {
    width: 80px;
    height: 80px;
    perspective: 600px;
    flex-shrink: 0;
  }

  .coin {
    width: 100%;
    height: 100%;
    position: relative;
    transform-style: preserve-3d;
    border-radius: 50%;
  }

  .coin.spinning {
    animation: coinSpin 0.25s linear infinite;
  }

  .coin-face {
    position: absolute;
    width: 100%;
    height: 100%;
    backface-visibility: hidden;
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 1.5rem;
    font-weight: 800;
    border: 3px solid #b45309;
  }

  .coin-front {
    background: linear-gradient(135deg, #fbbf24 0%, #d97706 50%, #b45309 100%);
    color: #78350f;
    transform: rotateY(0deg);
  }

  .coin-back {
    background: linear-gradient(135deg, #fde68a 0%, #fbbf24 50%, #d97706 100%);
    color: #78350f;
    transform: rotateY(180deg);
  }

  @keyframes coinSpin {
    0% {
      transform: rotateY(0deg);
    }
    100% {
      transform: rotateY(360deg);
    }
  }

  .reel-window {
    width: 64px;
    height: 96px;
    overflow: hidden;
    border-radius: 12px;
    background: #0f172a;
    border: 2px solid #1e293b;
    position: relative;
    flex-shrink: 0;
  }

  .reel-window::before,
  .reel-window::after {
    content: '';
    position: absolute;
    left: 0;
    right: 0;
    height: 24px;
    z-index: 10;
    pointer-events: none;
  }

  .reel-window::before {
    top: 0;
    background: linear-gradient(to bottom, rgba(15, 23, 42, 0.95), transparent);
  }

  .reel-window::after {
    bottom: 0;
    background: linear-gradient(to top, rgba(15, 23, 42, 0.95), transparent);
  }

  .reel {
    display: flex;
    flex-direction: column;
    align-items: center;
  }

  .reel.spinning {
    animation: reelSpin 0.1s linear infinite;
  }

  .reel-digit {
    height: 96px;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 2rem;
    font-weight: 700;
    color: #f8fafc;
    text-shadow: 0 0 12px rgba(248, 250, 252, 0.4);
    user-select: none;
  }

  @keyframes reelSpin {
    0% {
      transform: translateY(0);
    }
    100% {
      transform: translateY(-960px);
    }
  }

  @media (min-width: 768px) {
    .coin-scene {
      width: 96px;
      height: 96px;
    }

    .coin-face {
      font-size: 1.875rem;
      border-width: 4px;
    }

    .reel-window {
      width: 76px;
      height: 96px;
    }

    .reel-digit {
      height: 96px;
      font-size: 2.25rem;
    }
  }
</style>
