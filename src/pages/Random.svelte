<script lang="ts">
  let isSpinning = $state(false);
  let finalNumber = $state<number | null>(null);
  let displayedNumber = $state<number | null>(null);

  let coinEl: HTMLDivElement;
  let reelEls = $state<HTMLDivElement[]>([]);

  const DIGIT_HEIGHT = 80;
  const REEL_CYCLE = DIGIT_HEIGHT * 10;

  function getRandomKural(): number {
    return Math.floor(Math.random() * 1330) + 1;
  }

  function pad4(n: number): string {
    return n.toString().padStart(4, '0');
  }

  function getFinalCoinRotation(targetDigit: number, currentAngle: number): number {
    // targetDigit is 0 or 1
    // Face 0 is at multiples of 360, face 1 is at 180 + multiples of 360
    const base = targetDigit === 0 ? 0 : 180;
    // Find nearest landing angle that is >= currentAngle - 360 (to allow a little rewind)
    // but we want it to feel like it keeps spinning forward, so add several full rotations
    const minTarget = currentAngle + 360;
    const k = Math.ceil((minTarget - base) / 360);
    return base + k * 360;
  }

  function getFinalReelOffset(targetDigit: number, currentOffset: number): number {
    const digitOffset = targetDigit * DIGIT_HEIGHT;
    // Spin through 4-8 full cycles from current position
    const extraCycles = 4 + Math.floor(Math.random() * 4);
    const target = currentOffset + extraCycles * REEL_CYCLE + digitOffset;
    // Round to a full cycle boundary + digit offset so it lands cleanly
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

    // Spin for exactly 3 seconds
    await new Promise((r) => setTimeout(r, 3000));

    // Stop the coin
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

    // Stop the reels
    reelEls.forEach((el, i) => {
      if (!el) return;
      const computed = getComputedStyle(el);
      const matrix = new DOMMatrix(computed.transform);
      const currentOffset = -matrix.m42; // m42 is translateY
      const targetDigit = parseInt(digits[i + 1]);
      const finalOffset = getFinalReelOffset(targetDigit, currentOffset);
      el.style.transform = `translateY(-${currentOffset}px)`;
      el.classList.remove('spinning');
      requestAnimationFrame(() => {
        el.style.transition = 'transform 0.8s cubic-bezier(0.23, 1, 0.32, 1)';
        el.style.transform = `translateY(-${finalOffset}px)`;
      });
    });

    // Show result after settling
    await new Promise((r) => setTimeout(r, 900));
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

<section class="min-h-screen bg-gray-50 px-6 py-20">
  <div class="mx-auto max-w-3xl text-center">
    <p class="text-sm font-semibold uppercase tracking-wider text-red-600">தற்செயல் குறள்</p>
    <h1 class="mt-2 text-3xl font-bold text-gray-900 md:text-4xl">Random Thirukural</h1>
    <p class="mx-auto mt-4 max-w-lg text-gray-600">Let fate choose a Kural for you. Spin the reels and discover ancient wisdom.</p>

    <div class="mt-12 inline-flex items-center gap-3 rounded-2xl bg-white p-6 shadow-lg shadow-gray-200/50 md:gap-4 md:p-8">
      <!-- Coin (first digit) -->
      <div class="coin-scene">
        <div bind:this={coinEl} class="coin" class:spinning={isSpinning}>
          <div class="coin-face coin-front">0</div>
          <div class="coin-face coin-back">1</div>
        </div>
      </div>

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

    <button
      onclick={generate}
      disabled={isSpinning}
      class="mt-10 inline-flex items-center justify-center rounded-full bg-red-600 px-8 py-4 text-base font-semibold text-white shadow-lg shadow-red-200 transition-all hover:bg-red-700 hover:shadow-xl hover:shadow-red-200 active:scale-95 disabled:cursor-not-allowed disabled:opacity-60 md:text-lg"
    >
      {#if isSpinning}
        <svg class="mr-2 h-5 w-5 animate-spin" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24">
          <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
          <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4z"></path>
        </svg>
        Spinning...
      {:else}
        Generate Random Kural
      {/if}
    </button>

    {#if displayedNumber !== null}
      <div class="mt-10">
        <div class="inline-block rounded-2xl bg-white px-10 py-8 shadow-lg shadow-gray-200/50">
          <p class="text-sm font-medium uppercase tracking-wider text-gray-500">Kural Number</p>
          <p class="mt-2 text-6xl font-bold text-red-600 md:text-7xl">{displayedNumber}</p>
          <p class="mt-3 text-lg text-gray-400">{pad4(displayedNumber)}</p>
        </div>
      </div>
    {/if}
  </div>
</section>

<style>
  .coin-scene {
    width: 72px;
    height: 72px;
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
    animation: coinSpin 0.08s linear infinite;
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
    font-size: 1.75rem;
    font-weight: 700;
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
    width: 56px;
    height: 80px;
    overflow: hidden;
    border-radius: 12px;
    background: #fff;
    border: 2px solid #e5e7eb;
    position: relative;
    flex-shrink: 0;
  }

  .reel-window::before,
  .reel-window::after {
    content: '';
    position: absolute;
    left: 0;
    right: 0;
    height: 20px;
    z-index: 10;
    pointer-events: none;
  }

  .reel-window::before {
    top: 0;
    background: linear-gradient(to bottom, rgba(255, 255, 255, 0.9), transparent);
  }

  .reel-window::after {
    bottom: 0;
    background: linear-gradient(to top, rgba(255, 255, 255, 0.9), transparent);
  }

  .reel {
    display: flex;
    flex-direction: column;
    align-items: center;
  }

  .reel.spinning {
    animation: reelSpin 0.12s linear infinite;
  }

  .reel-digit {
    height: 80px;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 2rem;
    font-weight: 700;
    color: #1f2937;
    user-select: none;
  }

  @keyframes reelSpin {
    0% {
      transform: translateY(0);
    }
    100% {
      transform: translateY(-800px);
    }
  }
</style>
