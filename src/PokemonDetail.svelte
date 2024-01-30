<!--PokemonDetail.svelte-->
<script>
  import { onMount } from "svelte";
  import Types from "./components/Types.svelte";
  import { pokemonStore } from "./store.js";

  let stats;
  let selectedAbility = null;
  let abilityDescription = "";
  

  //togglezeug
  let isAbilityOpen = false;

  let pokemon;
  pokemonStore.subscribe((value) => {
    pokemon = value;
  });

  //englische info aus ability url holen
  async function fetchAbilityDescription(abilityUrl) {
    const response = await fetch(abilityUrl);
    const abilityData = await response.json();
    if (abilityData.effect_entries.length >= 2) {
      return abilityData.effect_entries[1].effect;
    } else {
      throw new Error("No description found for this ability.");
    }
  }

  async function selectAbility(ability) {
 selectedAbility = ability;
 abilityDescription = await fetchAbilityDescription(ability.ability.url);
 isAbilityOpen = !isAbilityOpen; // Toggle the isAbilityOpen variable
 console.log(abilityDescription); // Log the ability description
}


  export let params = {};
  let generation = "";
  let habitat = "";
  let shape = "";
  let color = "";
  let baseHappiness = 0;

  let heldItemsImages = [];
  let isLoaded = false;
  let isOpen = false;
  export let generations = [];

  let id = params.id;
  let flavorText = "";

  onMount(async () => {
    try {
      const response = await fetch(`https://pokeapi.co/api/v2/pokemon/${id}`);
      pokemon = await response.json();
      stats = params.stats;
      //held_items
      if (pokemon.held_items) {
        for (let item of pokemon.held_items) {
          const itemResponse = await fetch(item.item.url);

          const itemData = await itemResponse.json();
          if (itemData.sprites && itemData.sprites.default) {
            let englishName = "";
            for (let nameObj of itemData.names) {
              if (nameObj.language.name === "en") {
                englishName = nameObj.name;
                break;
              }
            }
            console.log(englishName); // Log the English name of the item
            heldItemsImages.push(itemData.sprites.default);
          }
        }
      }

      //games
      if (pokemon.game_indices) {
        generations = pokemon.game_indices.map((index) => index.version.name);
      }
      isLoaded = true;

      //species
      const speciesResponse = await fetch(
        `https://pokeapi.co/api/v2/pokemon-species/${id}`
      );
      const speciesDetails = await speciesResponse.json();
      console.log(speciesDetails);

      generation = speciesDetails.generation.name;
      habitat = speciesDetails.habitat.name;
      shape = speciesDetails.shape.name;
      baseHappiness = speciesDetails.base_happiness;
      color = speciesDetails.color.name;
      console.log(pokemon);

      // Find the English flavor text
      for (let flavorEntry of speciesDetails.flavor_text_entries) {
        if (flavorEntry.language.name === "en") {
          flavorText = flavorEntry.flavor_text;
          break;
        }
      }
      // Check if the pokemon evolves from another species
      if (speciesDetails.evolves_from_species) {
        // Fetch the evolved-from species details
        const evolvesFromResponse = await fetch(
          speciesDetails.evolves_from_species.url
        );
        const evolvesFromDetails = await evolvesFromResponse.json();

        console.log(evolvesFromDetails.name); // Log the name of the evolved-from pokemon
      }
    } catch (error) {
      console.error("Failed to fetch pokemon details:", error);
    }
    // Add an event listener to the Back button
    const backButton = document.getElementById("backButton");
    if (backButton) {
      backButton.addEventListener("click", goBack);
    }
  });
  function goBack() {
    history.back(); // Use the history object to go back to the previous page
  }
</script>

<div class="pagewrapper">
    <div class="top">
    <button id="backButton" class="full-width-button">Back</button>
</div>
  {#if pokemon}
  
    <div class="titleboard">
        <div class="infocard">

            <div class="titlestyle">
      <div class="idnr">{pokemon.id.toString().padStart(3, "0")}</div>
      <div class="titlename">{pokemon.name}</div>
      <div class="types">
        <Types types={pokemon.types} />
      </div>
      <div>{generation}</div>
    </div>
      <div class="artwork">
        <img
          src={pokemon.sprites["other"]["official-artwork"].front_default}
          alt={pokemon.name}
        />
      </div>


      <div class="gamesinfo">
        <button on:click={() => (isOpen = !isOpen)} class="full-width-button"
          >Games</button
        >
        {#if isOpen && generations && generations.length > 0}
          <div class="generations">
            {#each generations as generation}({generation}){#if generations.indexOf(generation) !== generations.length - 1},
              {/if}{/each}
          </div>
        {:else if isOpen}
          <div>No generations found.</div>
        {/if}
      </div>

    </div>


<div class="textCard">
      <div class="wideinfo">
        <div class="flavortext textfeld">{flavorText}</div>
        <div class="spirits">
          <img src={pokemon.sprites.front_default} alt={pokemon.name} />
          <img src={pokemon.sprites.back_default} alt={pokemon.name} />
          <img src={pokemon.sprites.front_shiny} alt={pokemon.name} />
          <img src={pokemon.sprites.back_shiny} alt={pokemon.name} />
        </div>
        
      </div>

      <div class="doubleinfo textfeld">
        <div class="baseinfo">
            <h3>base stats</h3>
            <div class="statlinksrechts">
            <div class="statname">Base Experience:</div><div> {pokemon.base_experience}</div>
        </div>
        <div class="statlinksrechts">
            <div class="statname">Habitat: </div><div> {habitat}</div>
    </div>
    <div class="statlinksrechts">
            <div class="statname">Base Happiness: </div><div> {baseHappiness}</div>
            </div>
            <div class="statlinksrechts">
            <div class="statname">Species: </div><div> {pokemon.species.name}</div>
            </div>
            <div class="looksinfo">
                <h3>looks</h3>
                <div class="statlinksrechts">
                <div class="statname">weight:</div><div> {pokemon.weight}</div>
            </div>
                <div class="statlinksrechts">
                <div class="statname">height: </div><div>{pokemon.height}</div>
            </div>
                <div class="statlinksrechts">
                <div class="statname">Shape: </div><div>{shape}</div>
            </div>
                <div class="statlinksrechts">
                <div class="statname">Color: </div><div>{color}</div>
            </div>
              </div>
          </div>
          
          <div class="abilitinfo">
            <h3>Abilities</h3>
            {#if pokemon.abilities}
              {#each pokemon.abilities as ability}
                <button on:click={() => selectAbility(ability)}>
                  {ability.ability.name}
                </button>
                
              {/each}
              
            {:else}
              <p>No abilities available.</p>
            {/if}
            {#if isAbilityOpen && selectedAbility}
            <div class="ability-description textfeld">
              <h4>{selectedAbility.ability.name}</h4>
              <p>{abilityDescription}</p>
            </div>
          {/if}
          </div>

        </div>

      </div>

    </div>  
     


     
    
    


  {:else}
    <p>Loading...</p>
  {/if}
  <div class="held-items">
    {#each heldItemsImages as image (image)}
      <img src={image} alt="Held Item" />
    {/each}
  </div>
</div>

<style>
  @import url("https://fonts.googleapis.com/css2?family=Silkscreen:wght@400;700&display=swap");
  @import url("https://fonts.googleapis.com/css2?family=Oswald:wght@200..700&display=swap");

  .pagewrapper {
    font-family: "Silkscreen", sans-serif;
    background-color: #ffffff79;
    border-radius: 10px;
    margin: 50px auto;
    width: 60%;
    padding: 15px;
  }
  .top{
    display:flex;
  }

  .titleboard {
    display: flex;
    justify-content: space-around;
    column-gap: 50px;
  }
  .infocard{
    width: 300px;
  }

  .titlestyle{
    background: #f5f5fa;
  border: 0;
  border-radius: 8px;
  box-shadow: -5px -5px 15px 0 #fff,5px 5px 15px 0 #1d0dca17;
  box-sizing: border-box;
  color: #2a1f62;
  padding: 10px;
  }
  .generations{
    font-size: 12px;
  }
  p {
    font-family: "Oswald", sans-serif;
  }
  .titlename {

    font-size: 30px;
    background-color: #F6F6FE;
  }
  .wideinfo {
    height: 150px;
  }
  .textfeld{
    background-color: #f5f5fa;
    border-radius: 10px;
    padding: 15px;
    font-size: 15px;
  }
  .types {
   
  }
  .infopage {
    padding: 20px 5vw;
  }
  .flavortext {
    font-family: "Oswald", sans-serif;
  }
  .gamesinfo {
 
    margin: 10px;
  }

  .artwork {
    margin-top: 20px;
    margin-bottom: 20px;
 
  }
  
  .idnr {
    border: 2px solid #f9f9f9;
    border-radius: 10px;
    font-size: 20px;

  }
  .artwork img {
    width: 60%;
    height: auto;
  }
  .infopage {
    display: flex;
    justify-content: space-between;
    align-items: center;
  }

  .doubleinfo{
    display: flex;
    width: 100%;
    justify-content: space-around;
  }

  .baseinfo {
  width: 40%;
  font-size: 15px;

  }
  .statlinksrechts{
    display: flex;
    justify-content: space-between;
    background-color: #ffffff79;
    border-radius: 10px;
    margin-bottom: 5px;
    padding-top: 2px;
    padding-left: 7px;
    padding-right: 7px;
  }
  .statname{
    font-size:7px;
  }
  .looksinfo {

}
  .abilitinfo {
    display: flex;
    flex-direction: column;
    width: 40%;
  
  }

  .spirits {
    height: auto;
    width: 100%;
    display: flex;
    flex-direction: row;
    justify-content: space-around;
  }
  .held-items {
  }
  .textstuff {
    width: 500px;
  }
  .abilityname {
    background-color: #f9f9f9;
    border-radius: 10px;
    padding: 5px;
    margin: 5px 0;
  }
</style>
