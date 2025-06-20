<script lang="ts">
  import { onMount } from 'svelte';
  import { Tween } from 'svelte/motion';
  import { cubicOut } from 'svelte/easing';
  import { Save, DollarSign, Trash2 } from 'lucide-svelte';
  import Confetti from 'svelte-confetti';
  import * as m from '$paraglide/messages';

  let { goal = 0, current = 0, newAmount = '' } = $props();

  const tween = new Tween(0, { duration: 600, easing: cubicOut });

  $effect(() => {
    tween.target = current;
  });

  onMount(() => {
    const storedGoal = localStorage.getItem('savingGoal');
    if (storedGoal !== null) goal = parseFloat(storedGoal);
    const storedCurrent = localStorage.getItem('currentSavings');
    if (storedCurrent !== null) current = parseFloat(storedCurrent);
  });

  const percentage = $derived(goal > 0 ? Math.min(100, (current / goal) * 100) : 0);

  function setGoal() {
    goal = parseFloat(String(goal)) || 0;
    localStorage.setItem('savingGoal', goal.toString());
    if (current > goal) {
      current = goal;
      localStorage.setItem('currentSavings', current.toString());
    }
  }

  function addMoney() {
    const amount = parseFloat(newAmount);
    if (!isNaN(amount)) {
      current = Math.min(goal, current + amount);
      localStorage.setItem('currentSavings', current.toString());
    }
    newAmount = '';
  }

  function addMoneyWith(amount: number) {
    current = Math.min(goal, current + amount);
    localStorage.setItem('currentSavings', current.toString());
  }

  function clearAll() {
    goal = 0;
    current = 0;
    newAmount = '';
    localStorage.removeItem('savingGoal');
    localStorage.removeItem('currentSavings');
  }
</script>

<div class="font-['Press_Start_2P'] text-black m-6 py-6 sm:m-0">
  <div class="relative max-w-2xl mx-auto my-8 p-8 bg-white border-8 border-blue-500 rounded-lg shadow-[8px_8px_0_#f0f,-8px_-8px_0_#0ff] text-center">
    <div class="absolute -top-5 -left-5 w-10 h-10 border-4 border-pink-500 rotate-45"></div>
    <div class="absolute -bottom-5 -right-5 w-10 h-10 bg-cyan-400 clip-triangle"></div>

    <h1 class="mb-12 text-2xl uppercase text-pink-500 drop-shadow-[2px_2px_0_#0ff]">Save-O-Matic 9000</h1>

    <section class="mb-6">
      <h2 class="mb-2 text-xl uppercase text-pink-500 drop-shadow-[2px_2px_0_#0ff]">{m.savings_goal()}</h2>
      <input
        type="number"
        bind:value={goal}
        min="0"
        placeholder="500"
        class="bg-black text-green-400 border-4 border-pink-400 p-3 text-base w-40"
      />
      <button
        onclick={setGoal}
        class="inline-flex gap-1 items-center justify-center m-2 px-6 py-3 bg-yellow-300 text-black border-4 border-pink-500 text-sm cursor-pointer hover:bg-pink-500 hover:text-white"
      >
        <Save size={16} /> {m.save_goal()}
      </button>
    </section>

    {#if goal > 0}
      <section class="mb-6">
        <h2 class="mb-2 text-xl uppercase text-pink-500 drop-shadow-[2px_2px_0_#0ff]">{m.your_savings()}</h2>
        <input
          type="number"
          bind:value={newAmount}
          placeholder="0"
          class="bg-black text-green-400 border-4 border-pink-400 p-3 text-base w-40"
        />
        <button
          onclick={addMoney}
          class="inline-flex gap-1 items-center justify-center m-2 px-6 py-3 bg-yellow-300 text-black border-4 border-pink-500 text-sm cursor-pointer hover:bg-pink-500 hover:text-white"
        >
          <DollarSign size={16} /> {m.add_money()}
        </button>
        <button
          onclick={() => addMoneyWith(1)}
          class="inline-flex gap-1 items-center justify-center m-2 px-6 py-3 bg-yellow-300 text-black border-4 border-pink-500 text-sm cursor-pointer hover:bg-pink-500 hover:text-white"
        >
          +1 kr
        </button>
        <button
          onclick={() => addMoneyWith(5)}
          class="inline-flex gap-1 items-center justify-center m-2 px-6 py-3 bg-yellow-300 text-black border-4 border-pink-500 text-sm cursor-pointer hover:bg-pink-500 hover:text-white"
        >
          +5 kr
        </button>
        <button
          onclick={() => addMoneyWith(10)}
          class="inline-flex gap-1 items-center justify-center m-2 px-6 py-3 bg-yellow-300 text-black border-4 border-pink-500 text-sm cursor-pointer hover:bg-pink-500 hover:text-white"
        >
          +10 kr
        </button>
      </section>

      <section class="mb-6 space-y-6">
        <h2 class="mb-2 text-xl uppercase text-pink-500 drop-shadow-[2px_2px_0_#0ff]">{m.progress()}</h2>
        <div><progress max={goal} value={tween.current} class="w-full h-8 border-2 border-black bg-gray-200 [&::-webkit-progress-bar]:bg-gray-200 [&::-webkit-progress-value]:bg-green-500"></progress>
        <div class=" text-sm relative">
          {current} kr / {goal} kr ({Math.round(percentage)}%)
        </div>
        </div>

        {#if current >= goal}
        <div class="flex text-2xl justify-center relative text-pink-500 drop-shadow-[2px_2px_0_#0ff] uppercase">
          {m.goal_reached()}
          <div class="absolute left-1/2">
            <Confetti amount={50} />
          </div>
        </div>
        {:else}
        <div class="mb-2 uppercase text-pink-500 drop-shadow-[2px_2px_0_#0ff]">
          {m.remaining_amount({ amount: goal - current })}
        </div>
        {/if}
      </section>
    {/if}

    <button
      onclick={clearAll}
      class="inline-flex gap-1 items-center justify-center m-2 px-6 py-3 bg-red-500 text-white border-4 border-black text-sm cursor-pointer hover:bg-red-600"
    >
      <Trash2 size={16} /> {m.clear_all_data()}
    </button>
  </div>
</div>

<style>
  @import url('https://fonts.googleapis.com/css2?family=Press+Start+2P&display=swap');
  
  .clip-triangle {
    clip-path: polygon(0 0, 100% 0, 50% 100%);
  }
</style>
