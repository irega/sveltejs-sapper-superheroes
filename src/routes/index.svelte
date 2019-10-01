<script context="module">
  import SuperHero from "../models/SuperHero";

  export function preload({ params, query }) {
    return this.fetch(
      "https://cdn.rawgit.com/akabab/superhero-api/0.2.0/api/all.json"
    )
      .then(response => response.json())
      .then(data => {
        const superheroes = data.map(
          s => new SuperHero(s.id, s.name, s.images.sm)
        );
        return { superheroes, originalSuperHeroes: superheroes };
      });
  }
</script>

<script>
  import { onMount } from "svelte";
  import SuperHeroList from "../components/Search/SuperHeroList.svelte";
  import Filters from "../components/Search/Filters.svelte";
  import * as sapper from "@sapper/app";
  const { session } = sapper.stores();

  export let superheroes, originalSuperHeroes;
  let nameFilter = "";

  onMount(() => {
    if ($session && $session.superheroes) {
      superheroes = originalSuperHeroes = $session.superheroes;
    } else {
      session.set({ superheroes });
    }
  });

  $: {
    if (nameFilter) {
      superheroes = originalSuperHeroes.filter(
        os => os.name.toUpperCase().indexOf(nameFilter.toUpperCase()) !== -1
      );
    } else {
      superheroes = originalSuperHeroes;
    }
  }
</script>

<style>

</style>

<svelte:head>
  <title>Svelte Sapper SuperHeroes</title>
</svelte:head>

<Filters bind:name={nameFilter} name={nameFilter} />
<SuperHeroList
  {superheroes}
  on:superHeroSelected={event => sapper.goto(event.detail.id)} />
