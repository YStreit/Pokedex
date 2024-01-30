<script>
   import Types from './Types.svelte';

   export let types = [];
   let selectedType = null;
   let isOpen = false;

   function toggleMenu() {
       isOpen = !isOpen;
   }

   function setType(typeName) {
    selectedType = typeName;
}


   function selectAll() {
       selectedType = null;
   }
</script>

<div>
 <button on:click={toggleMenu} class="full-width-button">Filters</button>
 {#if isOpen}
      <div class="typefilters">
        <div>Types: </div>
        <Types {types} on:typeClicked={setType} />
      </div>
 {/if}
</div>

<style>
 .full-width-button {
      display: block;
      width: 100%;
 }
 .typefilters{
     display: flex;
 }
</style>
