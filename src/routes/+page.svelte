<script>
    let search = null
    let resultSearchPok = null

    let previewStats = {"HP": 0, "Atk": 0, "Dfs": 0, "SpAtk": 0, "SpDfs": 0, "Spd": 0}
    let previewSprite = null
    let previewName = null
    let previewType1 = null
    let previewType2 = null
    let previewMoves = null

    let moveInfo = null
    let moveType = null

    let selectedMoves = [null, null, null, null]

    let selectedPokemon = [null, null, null, null, null, null]

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
        let url = "https://pokeapi.co/api/v2/pokemon/"+searchWord;
        try {
            const response = await fetch(url);
            if (!response.ok) {
            throw new Error(`Response status: ${response.status}`);
            }

            const result = await response.json();
            console.log(result);
            resultSearchPok = result
        } catch (error) {
            console.error(error.message);
        }
    }

    async function searchAny(searchLarge){
        let url = searchLarge;
        console.log(url)
        try {
            const response = await fetch(url);
            if (!response.ok) {
            throw new Error(`Response status: ${response.status}`);
            }

            const result = await response.json();
            console.log(result);
            return result;
        } catch (error) {
            console.error(error.message);
        }
    }

    function pokemonStats(){
        previewStats.HP = resultSearchPok.stats[0].base_stat
        previewStats.Atk = resultSearchPok.stats[1].base_stat
        previewStats.Dfs = resultSearchPok.stats[2].base_stat
        previewStats.SpAtk = resultSearchPok.stats[3].base_stat
        previewStats.SpDfs = resultSearchPok.stats[4].base_stat
        previewStats.Spd = resultSearchPok.stats[5].base_stat
        previewSprite = resultSearchPok.sprites.front_default
        previewName = resultSearchPok.name
        previewMoves = resultSearchPok.moves
        moveInfo = null
        moveType = null
        selectedMoves = ["click to add", "click to add", "click to add", "click to add"]

        searchAny(resultSearchPok.types[0].type.url).then((result) => {
            previewType1 = result
        })
        .catch(console.error)
        if (resultSearchPok.types.length >= 2) {
            searchAny(resultSearchPok.types[1].type.url).then((result) => {
                previewType2 = result
            })
            .catch(console.error)
        }
        else
            previewType2 = null
    }

    function moveStats(move){
        searchAny("https://pokeapi.co/api/v2/move/" + move).then((result) => {
            moveInfo = result
            searchAny(moveInfo.type.url).then((result) => {
                console.log("success 1")
                moveType = result
            })
            .catch(console.error)
        })
        .catch(console.error)
    }

    function searchMove(searchWord){
        searchWord.preventDefault();
        let formData = new FormData(searchWord.target);
    
        // Hämta värdet från input-fältet med name="search"
        search = formData.get('search');
        console.log(search)
        moveStats(search)
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
            {#if resultSearchPok != null}
                <article class="pictures">
                            <div onclick={()=>pokemonStats()}>
                                <img src={resultSearchPok.sprites.front_default} alt={resultSearchPok.name}/>
                            </div>
                </article>
            {/if}
        </div>
        <div class="pokemonPreviewOuter">
            {#if previewName != null}
            <div class="previewTop">
                <p class="previewName">{previewName}</p>
            </div>
            <div class="pokemonPreviewInner">
                <div class="previewLeft">
                    <img src={previewSprite} alt={"Pokémon preview"} class="previewImage"/>
                    <div class="types">
                        {#if previewType1 != null}
                        <img src={previewType1.sprites["generation-ix"]["scarlet-violet"].name_icon} alt={"Pokémon preview"} class="type"/>
                        {/if}
                        {#if previewType2 != null}
                        <img src={previewType2.sprites["generation-ix"]["scarlet-violet"].name_icon} alt={"Pokémon preview"} class="type"/>
                        {/if}
                    </div>
                </div>
                <div class="previewMiddle">
                    <p class="hp" id="stats">HP: {previewStats.HP}</p>
                    <p class="atk" id="stats">Attack: {previewStats.Atk}</p>
                    <p class="dfs" id="stats">Defense: {previewStats.Dfs}</p>
                    <p class="spatk" id="stats">Sp Attack: {previewStats.SpAtk}</p>
                    <p class="spdfs" id="stats">Sp Defense: {previewStats.SpDfs}</p>
                    <p class="spd" id="stats">Speed: {previewStats.Spd}</p>
                </div>
                <div class="previewRight">
                    <div class="moveList">
                        {#each previewMoves as movePreview}
                            <div class="moveBox" onclick={()=>moveStats(movePreview.move.name)}>
                                <p class="moveName">{movePreview.move.name}</p>
                            </div>
                        {/each}
                    </div>
                    <div class="moveSearch">
                        <form onsubmit={searchMove}>
                            <input type="text" name="search" placeholder="or search any Move">
                        </form>
                    </div>
                    {#if moveType != null}
                    <div class="moveInfo">
                        <div class="moveType">
                            <img src={moveType.sprites["generation-viii"]["legends-arceus"].symbol_icon} alt="type: {moveInfo.type.name}"/>
                        </div>
                        <div class="moveNumbers">
                            <p class="moveStats">{moveInfo.name}</p>
                            <p class="moveStats">Power: {moveInfo.power}</p>
                            <p class="moveStats">PP: {moveInfo.pp}</p>
                            <p class="moveStats">Accuracy: {moveInfo.accuracy}</p>
                            <p class="moveStats">{moveInfo.damage_class.name}</p>
                        </div>
                    </div>
                    {/if}
                    <div class="chosenMoveBox">
                        {#each selectedMoves as selectedMove}
                            <p class="chosenMove" onclick={()=>{selectedMove = moveInfo.name}}>{selectedMove}</p>
                        {/each}
                    </div>
                </div>
            </div>
            
            {/if}
        </div>
    </div>
</main>

<style>
    .top{
        width: 100%;
        justify-self: center;
        justify-items: center;
        border-style: solid;
        padding-bottom: 10px;
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
    .pokemonPreviewOuter{
        display: flex;
        flex-direction: column;
        position: absolute;
        right: 10vh;
    }
    .pokemonPreviewInner{
        display: flex;
        flex-direction: row;
    }
    .types{
        display: flex;
        flex-direction: column;
    }
    .type{
        margin: 10px;
        margin-top: 15px;
        margin-bottom: 15px;
    }
    .previewImage{
        justify-self: center;
        align-self: center;
        width: 30vh;
        border-bottom-style: solid;
    }
    .previewName{
        font-family:Verdana, Geneva, Tahoma, sans-serif;
        font-size:xx-large;
    }
    .previewLeft{
        display: flex;
        flex-direction: column;
        border-style: solid;
        border-right-style: none;
        border-bottom-left-radius: 20px;
    }
    .previewMiddle{
        display: flex;
        flex-direction: column;
        border-style: solid;
    }
    .previewRight{
        border-style: solid;
        border-left-style: none;
        border-bottom-right-radius: 20px;
    }
    .moveList{
        height: 100px;
        width: 200px;
        overflow-y: scroll;
    }
    .moveBox{
        margin: 10px;
        border-style: solid;
        border-radius: 20px;
    }
    .moveName{
        justify-self: center;
        margin: 2px;
    }
    .moveInfo{
        display: flex;
        border-bottom-style: solid;
        padding: 10px;
    }
    .moveType{
        margin-right: 10px;
    }
    .moveStats{
        margin: 0px;
    }
    .moveSearch{
        padding: 10px;
        justify-items: center;
        border-bottom-style: solid;
        border-top-style: solid;
    }
    .chosenMoveBox{
        display: grid;
        grid-template-columns:repeat(2, 1fr);
        justify-content: space-evenly;
        align-self: end;
    }
    .chosenMove{
        border-style: solid;
        border-radius: 20px;
        min-width: 40px;
        min-height: 20px;
        padding: 2px;
        margin: 10px;
    }
    .previewTop{
        border-style: solid;
        justify-items: center;
        border-bottom-style: none;
        border-top-style: none;
    }
    #stats{
        padding: 5px;
        border-color: black;
        border-width: 2px;
        border-style: solid;
        border-radius: 10px;
        margin-left: 10px;
        margin-right: 10px;
        font-family:Verdana, Geneva, Tahoma, sans-serif;
    }
    .hp{background-color: #69DC12;}
    .atk{background-color: #EFCC18;}
    .dfs{background-color: #E86412;}
    .spatk{background-color: #14C3F1;}
    .spdfs{background-color: #4A6ADF;}
    .spd{background-color: #D51DAD;}
</style>