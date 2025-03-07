<script lang="ts">
  import AutoSuggest from "./lib/AutoSuggest.svelte";
</script>

<main>
  <h1>AutoSuggestion Input</h1>

  <div class="card">
    <AutoSuggest url="http://localhost:5000/items?q=" debounceMs={200} />
  </div>
</main>
