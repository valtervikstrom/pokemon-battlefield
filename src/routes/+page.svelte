<script>
    let search = null
    let resultSearch = null

    let previewStats = {"HP": 0, "Atk": 0, "Dfs": 0, "SpAtk": 0, "SpDfs": 0, "Spd": 0}
    let previewSprite = null

    function handleSubmit(e) {
    // Förhindra att sidan laddas om (standardbeteende för formulär)
    e.preventDefault();
    
    // Skapa ett FormData-objekt från formuläret
    let formData = new FormData(e.target);
    
    // Hämta värdet från input-fältet med name="search"
    search = formData.get('search');
    console.log(search)
    searchResult(search)
    }

    async function searchResult(searchWord){
        let url = "http://pokeapi.co/api/v2/pokemon/"+searchWord;
        try {
            const response = await fetch(url);
            if (!response.ok) {
            throw new Error(`Response status: ${response.status}`);
            }

            const result = await response.json();
            console.log(result);
            resultSearch = result
        } catch (error) {
            console.error(error.message);
        }
    }

    function pokemonStats(){
        previewStats.HP = resultSearch.stats[0].base_stat
        previewStats.Atk = resultSearch.stats[1].base_stat
        previewStats.Dfs = resultSearch.stats[2].base_stat
        previewStats.SpAtk = resultSearch.stats[3].base_stat
        previewStats.SpDfs = resultSearch.stats[4].base_stat
        previewStats.Spd = resultSearch.stats[5].base_stat
        previewSprite = resultSearch.sprites.front_default
    }
</script>

<main>
    <div class="top">
        <h1>Team Builder</h1>
        <form onsubmit={handleSubmit}>
            <input type="text" name="search" placeholder="Search a Pokémon">
        </form>
    </div>
    <div class="outer">
        <div class="results">
            {#if resultSearch != null}
                <article class="pictures">
                    {#each Object.entries(resultSearch.sprites) as sprites}
                        {#if typeof sprites[1] == "string"}
                            <div onclick={()=>pokemonStats()}>
                                <img src={sprites[1]} alt={resultSearch.name}/>
                            </div>
                        {/if}
                    {/each}
                </article>
            {/if}
        </div>
        <div class="pokemonPreview">
            {#if previewSprite != null}
                <img src={previewSprite} alt={"Pokémon preview"} class="previewImage"/>
            {/if}
            {#each Object.entries( previewStats) as base_stat}
                <p>{base_stat[0]}: {base_stat[1]}</p>
            {/each}
        </div>
    </div>
</main>

<style>
    .top{
        justify-self: center;
    }
    .outer{
        display: flex;
        flex-direction: row;
    }
    .results{
        display: flex;
        flex-direction: row;
    }
    .pictures{
        display: flex;
        flex-direction: row;
    }
    .pokemonPreview{
        display: flex;
        flex-direction: column;
        position: absolute;
        right: 10vh;
    }
    .previewImage{
        position: absolute;
        right: 10vh;
        width: 30vh;
    }
</style>