<script>
  import { onMount } from 'svelte';
  import { tweened } from 'svelte/motion';
  import { cubicOut } from 'svelte/easing';

  let goal = 0;
  let current = 0;
  let newAmount = '';

  // tweened store to animate progress changes
  const tweenedCurrent = tweened(0, { duration: 600, easing: cubicOut });

  // update tween whenever current changes
  $: tweenedCurrent.set(current);

  onMount(() => {
    const storedGoal = localStorage.getItem('savingGoal');
    if (storedGoal !== null) goal = parseFloat(storedGoal);
    const storedCurrent = localStorage.getItem('currentSavings');
    if (storedCurrent !== null) current = parseFloat(storedCurrent);
  });

  $: percentage = goal > 0 ? Math.min(100, (current / goal) * 100) : 0;

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
    if (!isNaN(amount) && amount > 0) {
      current = Math.min(goal, current + amount);
      localStorage.setItem('currentSavings', current.toString());
    }
    newAmount = '';
  }

  function resetSavings() {
    current = 0;
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

<div class="container">
  <h1>Kid's Savings Tracker</h1>

  <section>
    <h2>Set Your Saving Goal</h2>
    <input type="number" bind:value={goal} min="0" placeholder="Enter goal amount" />
    <button on:click={setGoal}>Save Goal</button>
  </section>

  {#if goal > 0}
    <section>
      <h2>Track Your Savings</h2>
      <input
        type="number"
        bind:value={newAmount}
        min="0"
        placeholder="Enter amount saved"
      />
      <button on:click={addMoney}>Add Money</button>
      <button on:click={resetSavings}>Reset Savings</button>
    </section>

    <section>
      <h2>Progress</h2>
      <progress max={goal} value={$tweenedCurrent}></progress>
      <div class="progress-info">
        {current} / {goal} ({Math.round(percentage)}%)
      </div>
    </section>
  {/if}

  <button class="clear" on:click={clearAll}>Clear All Data</button>
</div>

<style>
  @import url('https://fonts.googleapis.com/css2?family=Press+Start+2P&display=swap');

  :global(body) {
    background: #ffd6d6;
    font-family: 'Press Start 2P', monospace;
    color: #000;
    margin: 0;
    padding: 0;
  }

  .container {
    position: relative;
    max-width: 600px;
    margin: 2rem auto;
    padding: 2rem;
    background: #fff;
    border: 6px solid #00f;
    border-radius: 8px;
    box-shadow: 8px 8px 0 #f0f, -8px -8px 0 #0ff;
    text-align: center;
  }

  .container::before {
    content: '';
    position: absolute;
    top: -20px;
    left: -20px;
    width: 40px;
    height: 40px;
    border: 4px solid #f06;
    transform: rotate(45deg);
  }

  .container::after {
    content: '';
    position: absolute;
    bottom: -20px;
    right: -20px;
    width: 40px;
    height: 40px;
    background: #0ff;
    clip-path: polygon(0 0, 100% 0, 50% 100%);
  }

  h1,
  h2 {
    margin: 0.5rem 0;
    text-transform: uppercase;
    color: #f06;
    text-shadow: 2px 2px 0 #0ff;
  }

  section {
    margin-bottom: 1.5rem;
  }

  input {
    background: #000;
    color: #0f0;
    border: 4px solid #f0f;
    padding: 0.75rem;
    font-size: 1rem;
    width: 150px;
  }

  button {
    margin: 0.5rem;
    padding: 0.75rem 1.5rem;
    background: #ff0;
    color: #000;
    border: 4px solid #f06;
    font-size: 0.9rem;
    cursor: pointer;
    text-shadow: 1px 1px #fff;
  }

  button:hover {
    background: #f06;
    color: #fff;
  }

  .clear {
    background: #f00;
    color: #fff;
    border-color: #000;
  }

  progress {
    width: 100%;
    height: 2rem;
    border: 2px solid #000;
    background: #ddd;
  }

  progress::-webkit-progress-bar {
    background: #ddd;
  }

  progress::-webkit-progress-value {
    background: #0f0;
  }

  .progress-info {
    margin-top: 0.5rem;
    font-size: 0.9rem;
  }
</style>
