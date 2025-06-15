<script lang="ts">
  import { browser } from '$app/environment';
  import { setLocale } from '$paraglide/runtime';
  import '../app.css';

  let { children } = $props();
  
  type Locale = 'en' | 'nb';
  
  // Set initial locale from browser or default to English
  if (browser) {
    const savedLocale = (localStorage.getItem('locale') || 'en') as Locale;
    setLocale(savedLocale);
  }

  function switchLanguage(locale: Locale) {
    setLocale(locale);
    if (browser) {
      localStorage.setItem('locale', locale);
    }
  }
</script>

<main class="min-h-screen">
  <div class="flex justify-end p-6">
    <div class="flex items-center gap-2 bg-white/80 backdrop-blur-sm px-3 py-2 rounded-lg border-2 border-pink-500">
      <button
        onclick={() => switchLanguage('en')}
        class="text-2xl transition-all cursor-pointer hover:scale-125 hover:drop-shadow-[0_0_8px_rgba(0,0,0,0.3)]"
        title="English"
      >
        🇬🇧
      </button>
      <span class="text-pink-500 font-bold">/</span>
      <button
        onclick={() => switchLanguage('nb')}
        class="text-2xl transition-all cursor-pointer hover:scale-125 hover:drop-shadow-[0_0_8px_rgba(0,0,0,0.3)]"
        title="Norsk"
      >
        🇳🇴
      </button>
    </div>
  </div>
  {@render children()}
</main>