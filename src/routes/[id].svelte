<script context="module">
  import { default as SuperHeroDetailModel } from "../models/SuperHeroDetail";

  export function preload({ params, query }) {
    return this.fetch(
      `https://cdn.rawgit.com/akabab/superhero-api/0.2.0/api/id/${params.id}.json`
    )
      .then(response => response.json())
      .then(data => ({
        selectedSuperHero: new SuperHeroDetailModel(
          data.id,
          data.name,
          data.images.sm,
          data.biography.fullName,
          data.appearance.gender,
          data.appearance.hairColor,
          data.appearance.race,
          data.appearance.eyeColor
        )
      }))
      .catch(err => ({ selectedSuperHero: null }));
  }
</script>

<script>
  import { onMount } from "svelte";
  import SuperHeroDetail from "../components/Search/SuperHeroDetail.svelte";
  import * as sapper from "@sapper/app";
  const { session, page } = sapper.stores();
  const { id } = $page.params;

  export let selectedSuperHero;

  onMount(() => {
    if (selectedSuperHero === null) {
      selectedSuperHero = getSuperHeroDetailFromStore(parseInt(id));
    }
  });

  function getSuperHeroDetailFromStore(heroId) {
    let detail = null;
    const superHero =
      $session && $session.superheroes
        ? $session.superheroes.find(s => s.id === heroId)
        : null;
    if (superHero) {
      detail = new SuperHeroDetailModel(superHero.id, superHero.name);
    }
    return detail;
  }
</script>

<SuperHeroDetail {selectedSuperHero} on:closeDetail={() => sapper.goto('/')} />
