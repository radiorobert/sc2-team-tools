<script>
 import Icon from '@iconify/svelte'
 export let players

 const dsGamemodeOptions = ['Commanders', 'Normal']
 let selectedGamemode = 'Commanders'

 console.log(players)
 const heroes = ["Abathur", "Alarak", "Artanis", "Dehaka", "Fenix", "Han & Horner", "Karax", "Kerrigan", "Mengsk", "Nova", "Raynor", "Stetmann", "Stukov", "Swann", "Tychus", "Vorazun", "Zagara"];
 let dsOptions = {dsUnique: true, dsNoGlobalRepeat: false, dsNoPlayerRepeat: true }

     console.log("lets go!")

 function randomRace() {

 }

 // Reroll something
 function rerollDS(idx) {
    dsHeroSelect(idx, [...heroes])
 }

 // Assign a Direct Strike Hero to all Players
 function dsAssignAll() {
      // Save these so that we can keep our other things clean
     let tmpHeroes = [...heroes];

     // Pop something off heroes, put it onto player. Move current hero if not null to hero hist
	 players.forEach((p, idx) => {
       dsHeroSelect(idx, tmpHeroes)
	 })

     console.log(players)
 }

 function dsHeroSelect(idx, tmpHeroes) {

       // Add previous hero to the history
       if(players[idx].hero) {
         players[idx].heroHistory.push(players[idx].hero)
       }

       let randInt = Math.floor(Math.random() * tmpHeroes.length)

       while (players[idx].heroHistory.includes(tmpHeroes[randInt])) {
         randInt = Math.floor(Math.random() * tmpHeroes.length);
       }

       console.log(randInt)
       console.log(tmpHeroes)

       // when we found a new hero they haven't played, pop it, so no one else gets it
       if(dsOptions.dsUnique) {
        players[idx].hero = tmpHeroes.splice(randInt, 1)
       } else {
        players[idx].hero = heroes[randInt]
    }

 }

 function reset() {
     console.log(heroes)
     players.forEach(p => {
         p.heroHistory = []
         p.hero = null
     })

    // reassign to trigger a state update
    players = players
 }

</script>

<main>
    {#each dsGamemodeOptions as g}
            <input
                type="radio"
                bind:group={selectedGamemode}
                name="ds-gamemode"
                value={g}
            > {g}
    {/each}
    <br/>
    <button on:click={reset}>Reset</button>
  <button on:click={dsAssignAll}>Pick Direct Strike Heroes</button>
  <label>
      <input type="checkbox" bind:checked={dsOptions.dsUnique} />
      Unique Heroes
  </label>
  <label>
      <input type="checkbox" bind:checked={dsOptions.dsNoPlayerRepeat} />
      No repeat per player
  </label>
  {#if players[0].hero}
  <ul>
      {#each players as p, idx}
          <li><button on:click={() => rerollDS(idx)}><Icon icon="openmoji:game-die"/></button>{p.player}: {p.hero}</li>
      {/each}
  </ul>
  {/if}
</main>

<style>
  input {
      background-color: gray;
  }
</style>
