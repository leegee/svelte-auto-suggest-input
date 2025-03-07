<script lang="ts">
    import { writable } from "svelte/store";

    export let url: string = "";

    export let debounceMs: number = 300;

    type Suggestion = {
        id: number;
        name: string;
    };

    let query = "";
    const suggestions = writable<Suggestion[]>([]);
    const showSuggestions = writable(false);

    let debounceTimeout: ReturnType<typeof setTimeout>;

    const fetchSuggestions = async () => {
        if (query.trim().length > 0) {
            const response = await fetch(url + query);
            const data = await response.json();
            suggestions.set(data);
            showSuggestions.set(data.length > 0);
        } else {
            suggestions.set([]);
            showSuggestions.set(false);
        }
    };

    const handleInput = (e: Event) => {
        query = (e.target as HTMLInputElement).value;

        // Clear the previous timeout if it's still active
        clearTimeout(debounceTimeout);

        // Set a new timeout to call fetchSuggestions after 300ms
        debounceTimeout = setTimeout(() => {
            fetchSuggestions();
        }, debounceMs);
    };

    const handleSelect = (item: Suggestion) => {
        query = item.name;
        showSuggestions.set(false);
    };
</script>

<main>
    <input
        type="search"
        id="q"
        bind:value={query}
        on:input={handleInput}
        placeholder="Search..."
        autocomplete="off"
    />

    {#if $showSuggestions}
        <div class="suggestions">
            {#each $suggestions as item}
                <button
                    type="button"
                    class="suggestion-item"
                    on:click={() => handleSelect(item)}
                >
                    {item.name}
                </button>
            {/each}
        </div>
    {/if}
</main>

<style>
    :root {
        --border-width: 1pt;
    }

    main {
        width: 30em;
        border: var(--border-width) solid grey;
        padding: 1em;
    }

    #q {
        border: var(--border-width) solid #ccc;
        width: calc(100% - 10pt);
        background-color: #004;
        padding: 5pt;
    }

    .suggestion-item {
        padding: 5pt;
        cursor: pointer;
        width: calc(100% - 2 * var(--border-width));
        text-align: left;
        margin: 0 var(--border-width);
    }

    .suggestion-item:hover {
        background-color: #006;
    }
</style>
