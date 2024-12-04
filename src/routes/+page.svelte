<script>
	import welcomeFallback from '$lib/images/svelte-welcome.png';
	let pokemons = $state([]) 
  fetch('https://pokeapi.co/api/v2/pokemon?limit=151').then(response => {
        return response.json()
  }).then(result => {
        pokemons = result.results
  })
	let selected = $state();
	let selectedPokemon = $state();
	const onSelect = (id) => {
		selected = id;
	}
	$effect(() => {
		fetch('https://pokeapi.co/api/v2/pokemon/' + selected).then(response => {
        return response.json()
  }).then(result => {
        selectedPokemon = result
				console.log(selectedPokemon)
  })
	})
	let search = $state();
	let filteredPokemons = $derived(search?pokemons.filter(pokemon => pokemon.name.includes(search)):pokemons)
	let information = $state();

</script>

<svelte:head>
	<title>Pokedex</title>
	<meta name="description" content="Svelte demo app" />
</svelte:head>

<section>
	<h1>
		<span class="pokedex">
		</span>
	</h1>
	<input bind:value={search}/>
	<div class="main">
		<div class=imagecontainer>
		{#if selectedPokemon}
		<img class="imgpkmn" src={selectedPokemon.sprites.front_default}/>
		{/if}
		</div>
      <ul class="pokelist">
          {#each filteredPokemons as pokemons}
              <li class="list" on:click={()=>{onSelect(pokemons.name)}} active={pokemons.name===selected}>
                  {pokemons.name}
              </li>
          {/each}
      </ul>
    </div>
	

</section>

<style>
	section {
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		flex: 0.6;
	}

	h1 {
		width: 100%;
	}

	.pokedex {
		display: block;
		position: relative;
		width: 100%;
		height: 0;
		padding: 0 0 calc(100% * 495 / 2048) 0;
	}

	.pokedex img {
		position: absolute;
		width: 100%;
		height: 100%;
		top: 0;
		display: block;
	}

	.main{
		display: grid;
		gap: 12px;
		grid-template-columns: 1fr 150px
	}
	
  .imagecontainer{
    display: flex;
    justify-content: center;
    align-items: center;
    width: 150px;
    height: 150px;
    background: linear-gradient(135deg, #ff0000, #ffaaaa);
    border: 4px solid #000;
    border-radius: 16px;
    box-shadow: 0px 4px 6px rgba(0, 0, 0, 0.2);
    position: relative;
  }

	.imgpkmn {
		width: 128px;
		height: 128px;
	}

	.list{
		padding: 8px;
		color: white;
    background-color: black;
	}

	.list[active="true"] {
  background-color: white;
	color: black;
	font-weight: bold;
}

	.pokelist {
		list-style: none;
		overflow-y: scroll;
		max-height: 400px;
	}
</style>
