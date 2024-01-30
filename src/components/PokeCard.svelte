<!--Pokecard.svelte-->
<script>
     import { createEventDispatcher } from 'svelte';
     import { pokemonStore } from '../store.js';

export let name = '';
export let details = {};
export let color = '';
export let currentPokemon = {};
export let gameIndices = [];
export let generations = []; // Add this line
export let locationAreas = [];
export let homePage = false;
import Types from './Types.svelte';
export let className = '';
let colorClasses = {
        'red': 'red-border',
        'blue': 'blue-border',
        'green': 'green-border',
        'white': 'white-border',
        'brown': 'brown-border',
        'yellow': 'yellow-border',
        'purple': 'purple-border',
        'pink': 'pink-border',
        'gray': 'gray-border',
        'black': 'black-border',
    
    };

const dispatch = createEventDispatcher();

function handleClick() {
    if (homePage) {
        window.location.hash = `/pokemon/${details.id}`;
    } else {
        dispatch('select', { detail: { name, details, color, gameIndices, generations } });
    }
 }
   </script>

<div class="card {className}" on:click={handleClick}>
    
    <div>{details.id.toString().padStart(3, "0")}</div>
    
    <h2 class="name">{name}</h2>


        <div class="cardimg">
            <img
            src={details.sprites["other"]["official-artwork"].front_default}
            alt={name}
            />
        </div>
        <div class="typen">
        <Types types={details.types} /></div>
        <!--
        {#each locationAreas as locationArea}
        <li>{locationArea.location.name}</li>
    {/each}-->
        <!--
        <div>Generations: {#each generations as generation}({generation}){#if generations.indexOf(generation) !== generations.length - 1}, {/if}{/each}</div>
-->
      <div class="infowrapper">
        <div class="info exp">
            <div class="txt">experience</div>
            <div class="nr">{details.base_experience}</div>
        </div>
        
         <div class="info hp">
            <div class="txt">health points</div>
            <div class="nr">{details.stats["0"].base_stat}</div>
         </div>
         <div class="info atk">
            <div class="txt">attack</div>
            <div class="nr">{details.stats["1"].base_stat}</div>
         </div>
         <div class="info def">
            <div class="txt">defense</div>
            <div class="nr">{details.stats["2"].base_stat}</div>
         </div>
         <div class="extra info">
         <div class="info height">
            <div class="txt">height</div>
            <div class="nr">{details.height}</div>
         </div>
         <div class="info weight">
            <div class="txt">weight</div>
            <div class="nr">{details.weight}</div>
         </div>
        </div>
      </div>

      
</div>

<style>
    @import url('https://fonts.googleapis.com/css2?family=Silkscreen:wght@400;700&display=swap');

@media (min-width: 601px) {
    .card{
        border: 1px solid #ccc;
    border-radius: 20px;
    margin: 15px;
    padding: 5px;
    border-width: 7px;
    border-color: white;
    background: #f5f5fa;
    width: 225px;
    height: 350px;
    box-shadow: -10px -10px 30px 0 #fff,10px 10px 30px 0 #1d0dca17;
    transition: 0.3s;
    font-family: "Silkscreen", sans-serif;
    }
    .card:hover {
  border-color: #646cff;
    }
 

.card img {
    width: 60%;
    height: auto;
  }
.name{
    margin: 5px;
}
  .info{
    display: flex;
    justify-content: space-between;
    border: 1px solid #ccc;
  }
  .exp{
    border-top-right-radius: 10px;
    border-top-left-radius: 10px;
margin-top: 10px;
  }
  .txt{
    
    font-size: 10px;
    align-self: flex-end;
    margin-left: 5px;
  }
  .nr{
    margin-right: 5px;
  }
  .extra{
    display: flex;
    border: 0;
    margin-top: 10px;
  }
  .height{
    width:100%;
    border-bottom-left-radius: 10px;

  }
  .weight{
    width: 100%;
    border-bottom-right-radius: 10px;

  }

}


@media (max-width: 600px) {

    .card{
        border: 1px solid #ccc;
    border-radius: 20px;
    margin: 5px;
    border-width: 10px;
    border-color: white;
    width: 500px;
    height: 100%;
    padding: 10px;
    box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2);
    transition: 0.3s;
    font-family: "Silkscreen", sans-serif;
display: grid; 
grid-template-columns: 150px auto auto;
grid-template-rows: 40px 20px auto;

    }
    .card:hover {
  border-color: #646cff;

}
.cardimg{
    grid-row: 3/4;
    grid-column: 1/2;
}
.card img {
    width: 100px;
    height: auto;
  }
.name{
    margin: 5px;
    grid-row: 1/2;
    grid-column: 2/4;
}
.typen{
    grid-row: 2/3;
    grid-column: 2/4;
}
.infowrapper{
    grid-row: 3/4;
    grid-column: 2/4;
}
  .info{
   
    display: flex;
    justify-content: space-between;
    border: 1px solid #ccc;
  }
  .exp{
    border-top-right-radius: 10px;
    border-top-left-radius: 10px;
margin-top: 10px;
  }
  .txt{
    
    font-size: 10px;
    align-self: flex-end;
    margin-left: 5px;
  }
  .nr{
    margin-right: 5px;
    grid-row: 1;
    grid-column: 1;
  }
  .extra{
    display: flex;
    border: 0;
    margin-top: 10px;
  }
  .height{
    width:100%;
    border-bottom-left-radius: 10px;

  }
  .weight{
    width: 100%;
    border-bottom-right-radius: 10px;

  }
}

</style>