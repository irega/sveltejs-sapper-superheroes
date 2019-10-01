<script>
  import { onDestroy } from "svelte";
  import SuperHero from "../models/SuperHero";
  import * as sapper from "@sapper/app";
  const { session } = sapper.stores();

  let name, totalSuperHeroes;

  function createSuperHero() {
    if (!$session) {
      session.set({ superheroes: [] });
    }
    const superheroes = $session.superheroes;
    const lastId =
      superheroes.length > 0 ? superheroes[superheroes.length - 1].id : 0;
    superheroes.push(new SuperHero(lastId + 1, name, "", true));
    session.update(prev => {
      prev.superheroes = superheroes;
      return prev;
    });
  }

  const unsubscribe = session.subscribe(value => {
    totalSuperHeroes = value ? value.superheroes.length : 0;
  });

  onDestroy(unsubscribe);
</script>

<style>

</style>

<h1>SuperHero Creation</h1>
<label for="name">Name:</label>
<input type="text" name="name" bind:value={name} />
<button on:click={createSuperHero}>Create</button>
<h2>Total SuperHeroes: {totalSuperHeroes}</h2>
