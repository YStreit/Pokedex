<script>
    import { onMount } from 'svelte';
    export let gameIndices = [];
    export let generations = [];
    let isOpen = false;

    onMount(async () => {
        for (let index of gameIndices) {
            const versionResponse = await fetch(index.version.url);
            const versionData = await versionResponse.json();
            const generationResponse = await fetch(versionData.generation.url);
            const generationData = await generationResponse.json();
            generations = [...generations, generationData.name];
        }
    });

    function toggleOpen() {
        isOpen = !isOpen;
    }
    function capitalizeFirstLetter(string) {
    return string.charAt(0).toUpperCase() + string.slice(1);
}

</script>

<button on:click={toggleOpen}>Games</button>
{#if isOpen}
    <div class="gens">
        {#each generations as generation (generation)}
        <div class="generation">{capitalizeFirstLetter(generation)}</div>
        {/each}
    </div>
{/if}

<style>
    .gens {
        font-family:Arial, Helvetica, sans-serif;
        display: flex;
        flex-wrap: wrap;
    }
    .generation{
        font-size: 10px;
        border: 2px solid #E3E3E3; 
        border-radius: 10px;
        padding: 2px 10px 2px 10px;
        margin: 2px 5px 2px 5px;

    }
</style>
