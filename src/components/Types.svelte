<!-- Types.svelte -->
<script>
    export let types = [];
    const typeColors = {
       normal: "#CBC1B7",
       fighting: "#8A2C0F",
       flying: "#7D96E9",
       poison: "#A14099",
       ground: "#D9AE48",
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
   </script>
   
   <div class="types">
{#each types as type}
    {#if type.type}
        <div class="type" style="background-color: {typeColors[type.type.name]} ;">
            {type.type.name}
        </div>
    {/if}
{/each}

   </div>
   
   <style>
    .types {
       /* Add your styles here */
       display:flex;
       justify-content: center;
    align-items: center;
    display: flex;
    font-size: 10px;
    
    }
   
    .type {
       /* Add your styles here */
       width: 100%;
       color: white;
  margin: 2px;
       border-radius: 10px;
    background-color: black;
    
    }
   </style>
   