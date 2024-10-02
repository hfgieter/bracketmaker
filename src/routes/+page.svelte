<script>
    import Login from "./login.svelte";
    import { token } from "./stores";
    import { onMount } from 'svelte';

    let playlistId = "4DRUt9tIIOSu60xLWJeFYP";
    let tracks = [];
    let selectedTracks = [];

    // Abonneer op de waarde van de token store
    let authToken = "";
    const unsubscribe = token.subscribe(value => {
      authToken = value;
    });

    onMount(() => {
      fetchPlaylist(); // Voer fetchPlaylist uit
    });

    function fetchPlaylist() {
      fetch(`https://api.spotify.com/v1/playlists/${playlistId}/tracks`, {
        method: 'GET',
        headers: {
          'Authorization': 'Bearer ' + authToken,
        },
      })
        .then(response => {
          if (!response.ok) {
            throw new Error(`HTTP-fout! Status: ${response.status}`);
          }
          return response.json();
        })
        .then(data => {
          // titels van de tracks opslaan in de 'tracks' variabele
          tracks = data.items.map(item => ({
            name: item.track.name.length > 36 ? item.track.name.slice(0, 36) + '...' : item.track.name,
            id: item.track.id,
            isSelected: false,
          }));
        })
        .catch(error => console.error('Fout bij het ophalen van Spotify-gegevens:', error));
    }

    function toggleSelect(index) {
    tracks[index].isSelected = !tracks[index].isSelected;

    if (tracks[index].isSelected) {
      // Als het nummer geselecteerd is, voeg het toe aan de selectedTracks array
      selectedTracks = [...selectedTracks, tracks[index]];
    } else {
      // Als het nummer gedeselecteerd is, verwijder het uit de selectedTracks array
      selectedTracks = selectedTracks.filter(track => track.id !== tracks[index].id);
    }
}

  </script>

  <main>
    <Login />
    <h1>Mijn Spotify-afspeellijst</h1>

    <input bind:value={playlistId} placeholder="Voer nieuwe playlist ID in" />

    <button on:click={fetchPlaylist}>Vernieuw afspeellijst</button>

  <div class="lists-container">
  <div id = list1>
    <ul>
      {#each tracks as { name, id, isSelected }, index (id)}
        <li>
          <button
            on:click={() => toggleSelect(index)}
            class:selected={isSelected}
          >
            {name}

          </button>
        </li>
      {/each}
    </ul>
  </div>
  <div id = list2>
    <ul>
      {#each selectedTracks as { name, id }}
      <li>
        <button>{name}</button>
      </li>
      {/each}
    </ul>
  </div>
  </div>
  </main>

  <style>

  .lists-container {
    display: flex;
    justify-content: space-between;
    width: 100%; /* Zorgt ervoor dat de twee lijsten over de breedte van het scherm verdeeld worden */
    max-width: 800px; /* Limiteert de breedte van de container */
    margin-top: 20px;
  }

  #list1, #list2 {
    width: 45%; /* Geef elke lijst 45% van de breedte */
  }

  ul button {
        padding: 0;
    }
	ul {
    width: max-content;
		display: flex;
		flex-direction: column;
		flex-grow: 1;
		list-style: none;
    padding: 0;
	}
	ul li {
		flex-grow: 1;
		display: flex;
		flex-direction: column;
		justify-content: center;
		padding: 0rem;
		text-align: center;
	}
	ul li {
		position: relative;
		font-size: 0.95rem;
	}
  .selected {
    background-color: grey;
  }

</style>