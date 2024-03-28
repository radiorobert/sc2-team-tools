<script>
 import Icon from '@iconify/svelte'
 import DirectStrike from './lib/DirectStrike.svelte'

 let players = [
	 {player: "Robert", hero: null, heroHistory: [], team: null},
	 {player: "Stephen", hero: null, heroHistory: [], team: null},
	 {player: "Amos", hero: null, heroHistory: [], team: null},
	 {player: "Max", hero: null, heroHistory: [], team: null},
 ];

 let selectedGamemode = null

 let name = ""

 const gameModes = [{id: "ds", title: "Direct Strike"}, {id: "m", title: "Melee"}]
 const options = {"Direct Strike": ["Pick Direct Strike Heroes"]}

 let colors = {red: "red", blue: "blue", green: "green", yellow: "yellow"}
 let teams = {enabled: false, count: 2, color: [colors.red, colors.blue, colors.green, colors.yellow]}

 $: selectedGamemode === "ds" ? teams.count = 2 : null

 function renderGamemodeMenu() {
     switch(selectedGamemode) {
             case 'ds':
             return `<DirectStrike />`

             case 'm':
             return `<h2>Melee!</h2>`

             default:
             break
     }

 }

 function incrementTeamCount() {
    if(teams.count === 6) { return }
    teams.count++
    teams = teams
 }

 function decrementTeamCount() {
    if(teams.count === 2) { return }
    teams.count--
    teams = teams
 }

 function shuffleTeams() {
  for (let i = players.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1));
    [players[i], players[j]] = [players[j], players[i]];
  }
  players = players;
     setTeams()
 }

 function toggleTeams() {
    teams.enabled = true
    teams = teams
 }

 // Add a new player to this list
 function newPlayer() {
     if(name === "") { return }
     players = [...players, {player: name, hero: null, heroHistory: [], team: ""}]
     name = ""
 }

function setTeams() {
    // generate an array the length of players, fill it with equal amounts based on team count
    let groupSize = Math.ceil(players.length / teams.count)
    let colorIdx = 0
    players.forEach((p, idx) => {
      if(idx % groupSize === 0 && idx !== 0) {
        colorIdx++
      }
      p.team = teams.color[colorIdx]
    })

    players = players
}

 // Remove a player
 function removePlayer(index) {
    players = players.filter((p, i) => i !== index)
    console.log(players)
 }
</script>


<main>
    <h1>SC2 Utils</h1>
    <form on:submit|preventDefault={() => name = name}>
        <input bind:value={name} placeholder="enter your name" />
        <button on:click={newPlayer}>Add</button>
    </form>
    <h2>Current Players</h2>
    <div>
        <button on:click={setTeams}>Teams ({teams.count})</button>
        <button on:click={decrementTeamCount}>-</button>
        <button on:click={incrementTeamCount}>+</button>
        <button on:click={shuffleTeams}>Shuffle</button>

    </div>
    <div class="player-container">
        {#each players as p, index}
            <div class="player" style="background-color: {p.team};"><button on:click={() => removePlayer(index)}><Icon icon="mdi:x-ray"/></button>{p.player}</div>
        {/each}
    </div>

    {#each gameModes as g}
            <input
                type="radio"
                bind:group={selectedGamemode}
                name="gamemode"
                value={g.id}
            > {g.title}
    {/each}

    {#if selectedGamemode === "ds"}
        <DirectStrike players={players}/>
        <h1>Direct Strike!</h1>
    {/if}

</main>

<style>
	main {
		text-align: center;
		padding: 1em;
		max-width: 240px;
		margin: 0 auto;
	}

	h1 {
		color: #ff3e00;
		text-transform: uppercase;
		font-size: 2em;
		font-weight: 100;
	}

    ul {
        list-style: none;
    }

    li {

    }

    .player-container {
        width: 25%;
        margin: auto;
    }

    .player {
        display: flex;
        gap: 16px;
        align-items: center;
    }

    .player button {
        background: none;
        border: none;
    }

    Icon {
        padding: none;
        margin: none;
    }

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}
</style>
