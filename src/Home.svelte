<!--Home.svelte-->
<script>
    import { pokemonStore } from './PokemonStore.js';
    import PokeCard from "./components/PokeCard.svelte";
    import Types from "./components/Types.svelte";
    let isOpen = false;
    let isLoading = false;
    $: pokemon = [];
    let types = [];
    $: {
        if (pokemon.length > 0) {
            types = Array.from(new Set(pokemon.flatMap(poke => poke.details.types.map(type => type.type.name))));
        }
    }
    let locations = [];
    $: {
    if (pokemon.length > 0) {
        locations = Array.from(new Set(pokemon.flatMap(poke => poke.locationAreas.map(location => location.location.region.name))));
    } else {
        locations = [];
    }
}


let selectedLocation = null;
function setLocation(locationName) {
    selectedLocation = locationName;
}

    
    let generations = [];
    $: {
        if (pokemon.length > 0) {
            generations = Array.from(new Set(pokemon.flatMap(poke => poke.generations)));
        }
    }
    let colors = [];
$: {
    if (pokemon.length > 0) {
        colors = Array.from(new Set(pokemon.map(poke => poke.color)));
    } else {
        colors = [];
    }
}
let selectedColor = null;
function setColor(colorValue) {
    selectedColor = colorValue;
}

const typeKnopf = {
       'normal': "#CBC1B7",
       'fighting': "#8A2C0F",
       'flying': "#7D96E9",
       'poison': "#A14099",
       'ground': "#D9AE48",
       rock: "#A1812D",
       bug: "#849900",
       ghost: "#5E61B5",
       steel: "#B5B5C4",
       fire: "#D60000",
       water: "#009DF8",
       grass: "#51C100",
       electric: "#FFB700",
       psychic: "#FF2E80",
       ice: "#87E9FE",
       dragon: "#785FE7",
       dark: "#A1812D",
       fairy: "#FDAAFB",
       shadow: "#A8A77A",
       unknown: "#FF4500",
    };
    let selectedType = null;
    function setType(typeName) {
        selectedType = typeName;
    }
    let selectedGeneration = null;
    function setGeneration(generationNames) {
        selectedGeneration = generationNames;
    }
    function resetType() {
        selectedType = null;
        selectedColor = null;
        selectedLocation = null;
    }

    async function fetchPokemonDetails(url) {
    try {
        const response = await fetch(url);
        const details = await response.json();

        if (details.location_area_encounters) {
            const locationAreaEncountersResponse = await fetch(details.location_area_encounters);
            const locationAreaEncountersData = await locationAreaEncountersResponse.json();

            // Add the location area encounters data to the details object
            details.locationAreaEncounters = locationAreaEncountersData;

            // Fetch location areas and add the data to the details object
            const locationAreasPromises = locationAreaEncountersData.map(async (locationAreaEncounter) => {
                const locationAreaResponse = await fetch(locationAreaEncounter.location_area.url);
                const locationAreaData = await locationAreaResponse.json();

                // Fetch location data and add it to the locationAreaData object
                const locationResponse = await fetch(locationAreaData.location.url);
                const locationData = await locationResponse.json();
                locationAreaData.location = locationData;

                // Add location area data to the locationAreaEncounter object
                locationAreaEncounter.locationArea = locationAreaData;

                return locationAreaEncounter;
            });

            const locationAreaEncountersWithDetails = await Promise.all(locationAreasPromises);

            // Replace the original locationAreaEncounters with the updated ones
            details.locationAreaEncounters = locationAreaEncountersWithDetails;
        }

        return { details };
    } catch (error) {
        console.error("Failed to fetch pokemon details:", error);
        return { details: null, encounters: [] };
    }
}





async function fetchPokemon() {
    try {
        isLoading = true;
        let offset = 0;
        let limit = 20;
        pokemon = [];
        while (offset <= 2000) {
            const response = await fetch(`https://pokeapi.co/api/v2/pokemon/?offset=${offset}&limit=${limit}`);
            const data = await response.json();
            pokemon = [...pokemon, ...await Promise.all(data.results.map(async (poke) => {
                const { details } = await fetchPokemonDetails(poke.url);
                const speciesDetails = await fetchSpeciesDetails(details.species.url);
                let gameIndicesData = await Promise.all(
                    details.game_indices.map(async (gameIndex) => {
                        const versionResponse = await fetch(gameIndex.version.url);
                        const versionData = await versionResponse.json();
                        const versionGroupResponse = await fetch(versionData.version_group.url);
                        const versionGroupData = await versionGroupResponse.json();
                        return { ...gameIndex, version: versionData, versionGroup: versionGroupData };
                    })
                );
                let generationNames = gameIndicesData.map(gameIndex => gameIndex.versionGroup.generation.name);
                generationNames = Array.from(new Set(generationNames));
// Inside the fetchPokemon function
let maxHP = 0;
for (let poke of pokemon) {
 let hpStat = poke.details.stats.find(stat => stat.stat.name === 'hp').base_stat;
 if (hpStat > maxHP) {
    maxHP = hpStat;
 }
}

return { ...poke, details, color: speciesDetails.color.name, gameIndices: gameIndicesData, generations: generationNames, locationAreas: details.locationAreaEncounters.map(l => l.locationArea), maxHP };
            }))];
            offset += limit;
        }
        pokemonStore.set(pokemon);
    } catch (error) {
        console.error("Failed to fetch pokemon:", error);
    } finally {
        isLoading = false;
        console.log(pokemon); // Move the console.log here
    }
}
    
    async function fetchSpeciesDetails(url) {
        try {
            const response = await fetch(url);
            const details = await response.json();
            return details;
        } catch (error) {
            console.error("Failed to fetch species details:", error);
        }
    }
    fetchPokemon();
    console.log(pokemon);
</script>
    
    <div>
     <h1>Choose your Pokemon!</h1>
     <!--
     <Filters {types} {selectedType} on:typeClicked={(e) => selectedType = e.detail} />
    -->
    <div class="filtermenu">
    <button on:click={() => isOpen = !isOpen} class="full-width-button filterknopf">Filters</button>
    {#if isOpen}
        <div class="typefilters">
            <div>Types: </div>
            <div class="seitscrollbox">
            {#each types as type}
                <button
                class:active={type === selectedType}
                    class="type {typeKnopf[type]}"
                    on:click={() => setType(type)}
                >
                    {type}
                </button>
            {/each}
        </div>
            <div>Generations: </div>
            <div class="seitscrollbox">
            {#each generations as generation}
                <button
                    class="generation"
                    on:click={() => setGeneration(generation)}
                >
                    {generation}
                </button>
            {/each}
            </div>
            <div>Colors: </div>
            <div class="seitscrollbox">
            {#each colors as color}
                <button
                class:active={color === selectedColor}
                    class="color"
                    on:click={() => setColor(color)}
                >
                    {color}
                </button>
            {/each}
        </div>
            <div>Regions: </div>
            <div class="seitscrollbox">
        {#each locations as location}
            <button
            class:active={location === selectedLocation}
                class="location"
                on:click={() => setLocation(location)}
            >
                {location}
            </button>
        {/each}</div>
        </div>
        <button on:click={resetType}>Reset</button>

    {/if}
</div>
     <div class="cardwrapper">
     <!--eine karte pro pokemon anzeigen-->
     {#each pokemon as poke}
     {#if 
        (selectedType === null || poke.details.types.some(type => type.type.name === selectedType)) &&
        (selectedGeneration === null || poke.generations.some(gen => selectedGeneration.includes(gen))) &&
        (selectedLocation === null || poke.locationAreas.some(location => location.location.region.name === selectedLocation)) &&
        (selectedColor === null || poke.color === selectedColor)
    }
    
    
    
        <PokeCard
          name={poke.name}
          details={poke.details}
          color={poke.color}
          gameIndices={poke.details.game_indices}
          generations={poke.generations} 
          locationAreas={poke.locationAreas}
            homePage={true}
        />
     {/if}
    {/each}
    
    
    </div>
    </div>
<style>
    @media (min-width: 601px) {

    .cardwrapper{
        display: flex;
        flex-wrap: wrap;
        justify-content: space-around;
        margin: 30px 100px;
    }
    .active {
        border: 2px solid black; /* Change to your preferred border style */
    }
    .filtermenu{
        padding: auto;
      
    }
}
    @media (max-width: 600px) {
        .cardwrapper{
        display: flex;
        flex-wrap: wrap;
        justify-content: space-around;
        margin: 0 50px;
    }
    }
</style>