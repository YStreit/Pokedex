<script>
   import Router from 'svelte-spa-router';

 import About from './About.svelte';
 import Home from './Home.svelte';
 import Quiz from './Quiz.svelte'

 import PokemonDetail from './PokemonDetail.svelte';
import Header from "./components/Header.svelte";


const routes = {
   '/': Home,
   '/about': About,
   '/quiz': Quiz,

   '/pokemon/:id': PokemonDetail,
   // Other routes...
 }


</script>

<header>
  <Header />
  <Router {routes} />
</header>
<main>

</main>

<style>

</style>
