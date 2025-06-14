<script>
  let { children } = $props();
  import '../app.css';
</script>

<main class="min-h-screen bg-pink-100">
  {@render children()}
</main>