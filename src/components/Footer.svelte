<footer class="border-t border-silver/15 bg-black px-6 py-12">
  <div class="mx-auto max-w-7xl">
    <div class="flex flex-col items-center justify-between gap-6 md:flex-row">
      <button
        class="flex items-center gap-2 text-xl font-bold text-gold"
        onclick={() => window.scrollTo({ top: 0, behavior: 'smooth' })}
      >
        <svg class="h-7 w-7" viewBox="0 0 40 40" fill="none" xmlns="http://www.w3.org/2000/svg">
          <circle cx="20" cy="20" r="18" stroke="currentColor" stroke-width="3"/>
          <path d="M12 28L20 12L28 28" stroke="currentColor" stroke-width="3" stroke-linecap="round" stroke-linejoin="round"/>
          <circle cx="20" cy="22" r="3" fill="currentColor"/>
        </svg>
        <span class="font-noto">வள்ளுவன்</span>
      </button>

      <div class="flex gap-8 text-sm text-white/50">
        <a href="#features" class="hover:text-gold">Features</a>
        <a href="#competition" class="hover:text-gold">Competition</a>
        <a href="#prizes" class="hover:text-gold">Prizes</a>
        <a href="#contact" class="hover:text-gold">Contact</a>
      </div>

      <p class="text-sm text-white/40">© {new Date().getFullYear()} Valluvan. All rights reserved.</p>
    </div>
  </div>
</footer>
