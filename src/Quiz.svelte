<script>
    import { onMount } from 'svelte';

    let evolutionChains = [];
    let speciesName = ''; // New variable to store the name
    let pokepic1 = '';
    let pokepic2 = '';
    let pokepicw1 = '';
    let pokepicw2 = '';
    let evolvesToSpeciesData = null;
    let buttonVisible = true;
    let gameVisible = false;
    let colorData = null;

    let correctButton;
    let wrongButton1;
    let wrongButton2;
    let nextButtonVisible = false;

    let wrongClicks = 0; // Counter for wrong clicks
    const maxWrongClicks = 4; // Maximum allowed wrong clicks


    onMount(async () => {
        try {
            const response = await fetch('https://pokeapi.co/api/v2/evolution-chain');
            const data = await response.json();
            evolutionChains = data.results;
        } catch (error) {
            console.error('Error:', error);
        }
    });

    async function fetchRandomEvolutionChain() {
        try {
            const randomIndex = Math.floor(Math.random() * evolutionChains.length);
            const url = evolutionChains[randomIndex].url;

            // Fetch the first level data
            const response = await fetch(url);
            const data = await response.json();
            console.log('First Level Data:', data);

            // Check if species and species.url exist
            if (data && data.chain && data.chain.species && data.chain.species.url) {
                const speciesUrl = data.chain.species.url;

                // Fetch additional data from species.url
                const speciesResponse = await fetch(speciesUrl);
                const speciesData = await speciesResponse.json();
                console.log('Additional Data from Species URL:', speciesData);

                // Set the speciesName to the name from the data
                speciesName = speciesData.name;

                // Check if varieties and varieties[0].pokemon.url exist
                if (speciesData.varieties && speciesData.varieties.length > 0 && speciesData.varieties[0].pokemon.url) {
                    const pokemonUrl = speciesData.varieties[0].pokemon.url;

                    // Fetch additional data from pokemon.url
                    const pokemonResponse = await fetch(pokemonUrl);
                    const pokemonData = await pokemonResponse.json();
                    console.log('Additional Data from Pokemon URL:', pokemonData);
                    pokepic1 = pokemonData.sprites.front_default;
                } else {
                    console.warn('Unable to find varieties or varieties[0].pokemon.url in the response.');
                }

                // Check if chain.evolves_to[] exists
                if (data.chain.evolves_to && data.chain.evolves_to.length > 0 && data.chain.evolves_to[0].species.url) {
                    const evolvesToSpeciesUrl = data.chain.evolves_to[0].species.url;

                    // Fetch additional data from evolvesToSpeciesUrl
                    const evolvesToSpeciesResponse = await fetch(evolvesToSpeciesUrl);
                    evolvesToSpeciesData = await evolvesToSpeciesResponse.json();
                    console.log('Additional Data from evolves_to[0].species.url:', evolvesToSpeciesData);
                } else {
                    console.warn('Unable to find chain.evolves_to[] or chain.evolves_to[0].species.url in the response.');
                }
                // Check if varieties and varieties[0].pokemon.url exist
                if (evolvesToSpeciesData.varieties && evolvesToSpeciesData.varieties.length > 0 && evolvesToSpeciesData.varieties[0].pokemon.url) {
                    const evolvedPokemonUrl = evolvesToSpeciesData.varieties[0].pokemon.url;

                    // Fetch additional data from pokemon.url
                    const pokemonResponse = await fetch(evolvedPokemonUrl);
                    const evolvedPokemonData = await pokemonResponse.json();
                    console.log('Additional Data from evolved Pokemon URL:', evolvedPokemonData);
                    pokepic2 = evolvedPokemonData.sprites.front_default;
                } else {
                    console.warn('Unable to find varieties or varieties[0].pokemon.url in the response.');
                }

                // Check if color.url exists
                if (evolvesToSpeciesData.color && evolvesToSpeciesData.color.url) {
                    const colorUrl = evolvesToSpeciesData.color.url;

                    // Fetch additional data from color.url
                    const colorResponse = await fetch(colorUrl);
                    colorData = await colorResponse.json();
                    console.log('Additional Data from color.url:', colorData);
                } else {
                    console.warn('Unable to find color or color.url in the response.');
                }
                
                // Choose two random pokemon_species names from the array
                if (colorData && colorData.pokemon_species && colorData.pokemon_species.length > 1) {
                    const randomIndex1 = Math.floor(Math.random() * colorData.pokemon_species.length);
                    const randomIndex2 = getRandomIndexExcept(randomIndex1, colorData.pokemon_species.length);
                    
                    const pokemonSpecies1 = colorData.pokemon_species[randomIndex1];
                    const pokemonSpecies2 = colorData.pokemon_species[randomIndex2];

                    console.log('Random Pokemon Species 1:', pokemonSpecies1);
                    console.log('Random Pokemon Species 2:', pokemonSpecies2);

                    // Fetch the data from the pokemonSpecies1 URL
const pokemonSpecies1Response = await fetch(pokemonSpecies1.url);
const pokemonSpecies1Data = await pokemonSpecies1Response.json();
console.log('Data from pokemonSpecies1 URL:', pokemonSpecies1Data);

// Fetch the data from the pokemonSpecies2 URL
const pokemonSpecies2Response = await fetch(pokemonSpecies2.url);
const pokemonSpecies2Data = await pokemonSpecies2Response.json();
console.log('Data from pokemonSpecies2 URL:', pokemonSpecies2Data);

// Check if varieties and varieties[0].pokemon.url exist
if (pokemonSpecies1Data.varieties && pokemonSpecies1Data.varieties.length > 0 && pokemonSpecies1Data.varieties[0].pokemon.url) {
                    const pokemonUrl = pokemonSpecies1Data.varieties[0].pokemon.url;

                    // Fetch additional data from pokemon.url
                    const pokemonResponse = await fetch(pokemonUrl);
                    const pokemon1Data = await pokemonResponse.json();
                    console.log('Additional Data from Pokemon URL:', pokemon1Data);
                    pokepicw1 = pokemon1Data.sprites.front_default;

                } else {
                    console.warn('Unable to find varieties or varieties[0].pokemon.url in the response.');
                }
                // Check if varieties and varieties[0].pokemon.url exist
if (pokemonSpecies2Data.varieties && pokemonSpecies2Data.varieties.length > 0 && pokemonSpecies2Data.varieties[0].pokemon.url) {
                    const pokemonUrl = pokemonSpecies2Data.varieties[0].pokemon.url;

                    // Fetch additional data from pokemon.url
                    const pokemonResponse = await fetch(pokemonUrl);
                    const pokemon2Data = await pokemonResponse.json();
                    console.log('Additional Data from Pokemon URL:', pokemon2Data);
                    pokepicw2 = pokemon2Data.sprites.front_default;
                    
                } else {
                    console.warn('Unable to find varieties or varieties[0].pokemon.url in the response.');
                }
                } else {
                    console.warn('Unable to find pokemon_species array or it contains less than 2 elements.');
                }
                
            } else {
                console.warn('Unable to find species or species.url in the response.');
            }

        } catch (error) {
            console.error('Error:', error);
        }

        buttonVisible = false;
        gameVisible = true;
        

    }
    // Helper function to get a random index except the provided one
    function getRandomIndexExcept(excludeIndex, arrayLength) {
        let randomIndex = Math.floor(Math.random() * arrayLength);
        while (randomIndex === excludeIndex) {
            randomIndex = Math.floor(Math.random() * arrayLength);
        }
        return randomIndex;
    }

    let feedbackText = '';
    let showButtons = true;
    $: {
        if (correctButton) {
            correctButton.addEventListener('click', function() {
                this.classList.add('correct-clicked');
                feedbackText = 'Correct!';
                showButtons = false;
                nextButtonVisible = true;
            });
        }

        if (wrongButton1) {
            wrongButton1.addEventListener('click', function() {
                this.classList.add('wrong-clicked');
                feedbackText = 'wrong!';
                wrongClicks += 1;
            });
        }

        if (wrongButton2) {
            wrongButton2.addEventListener('click', function() {
                this.classList.add('wrong-clicked');
                feedbackText = 'wrong!';
                wrongClicks += 1;
            });
        }
        if (wrongClicks >= maxWrongClicks) {
                    showButtons = false; // End the game if the maximum wrong clicks are reached
                    feedbackText = 'Game Over - Too Many Wrong Clicks';
                }
    }
    $: {
        const lives = document.querySelectorAll('.life');
        lives.forEach((life, index) => {
            if (index < wrongClicks) {
                life.style.backgroundColor = 'red';
            } else {
                life.style.backgroundColor = 'white';
            }
        });
    }
    function resetGame() {
    showButtons = true;
    feedbackText = ''; // Clear the feedback text
    wrongClicks = 0;
        fetchRandomEvolutionChain();
    // Reset other variables as needed...
}
function nextPokemon() {
        nextButtonVisible = false; // Hide the next button
        feedbackText = ''; // Clear the feedback text
        fetchRandomEvolutionChain();
        showButtons = true;
}
function gameOver() {
        showButtons = false; // Hide the answer buttons
        feedbackText = 'Game Over - Too Many Wrong Clicks';
        nextButtonVisible = false; // Hide the next button
    }
</script>

<main>

    <div class="arena">
    {#if buttonVisible} <!-- Conditionally render the button -->
    <button on:click={fetchRandomEvolutionChain}>Random Evolution</button>
{/if}   

{#if gameVisible}
<h2>What does this Pokemon evolve to?</h2>
    <div class="poke1">
        
        <img src={pokepic1}/>
        <h3>{speciesName}</h3>
    </div>


    <div class="answers">
   
        <div class="evolvesToSpecies">
            <button id="correctButton" class="antwort correct" bind:this={correctButton}>
            <img src={pokepic2}/>
        </button>
        </div>
 

        {#if showButtons}
        <div class="wrong1">
            <button id="wrongButton1" class="antwort wrong" bind:this={wrongButton1}>
                <img src={pokepicw1}/>
            </button>
        </div>
        <div class="wrong2">
            <button id="wrongButton2" class="antwort wrong" bind:this={wrongButton2}>
                <img src={pokepicw2}/>
            </button>
        </div>
        
        {/if}
        {#if !showButtons}
        {#if nextButtonVisible}
        <button class="nextknopf" on:click={nextPokemon}>Next</button>
    {:else}
    {#if wrongClicks >= maxWrongClicks}
                <button on:click={resetGame}>Play Again</button>
            {/if}
{/if}

{/if}

    </div>
    {#if feedbackText}
    <p>{feedbackText}</p>
    {/if}
    <div class="lives">
        <p>Lives: </p>
        {#each Array(maxWrongClicks) as _, index}
            <div class="life"></div>
        {/each}
    </div>
{/if}

</div>

</main>

<style>

.answers{
    display: flex;
    justify-content: space-around;
}

.arena{
    margin-top: 100px;
}
.antwort {
  align-items: center;
  background: #f5f5fa;
  border: 0;
  border-radius: 8px;
  box-shadow: -10px -10px 30px 0 #fff,10px 10px 30px 0 #1d0dca17;
  box-sizing: border-box;
  color: #2a1f62;
  cursor: pointer;
  display: flex;
  font-family: "Cascadia Code",Consolas,Monaco,"Andale Mono","Ubuntu Mono",monospace;
  font-size: 1rem;
  justify-content: center;
  line-height: 1.5rem;
  padding: 15px;
  position: relative;
  text-align: left;
  transition: .2s;
  user-select: none;
  -webkit-user-select: none;
  touch-action: manipulation;
  white-space: pre;
  width: max-content;
  word-break: normal;
  word-spacing: normal;
}

.antwort:hover {
  background: #f8f8ff;
  box-shadow: -15px -15px 30px 0 #fff, 15px 15px 30px 0 #1d0dca17;
}

.correct-clicked {
    border: 2px solid green;
}

.wrong-clicked {
    border: 2px solid red;
    background: red;
}

.nextknopf{

}
.life{
    height: 10px;
    width: 10px; 
    background-color: white;
    border-radius:100px;

}
.lives{
    display: flex;
    justify-content: center;
    
    margin: 5px
    
}
</style>
