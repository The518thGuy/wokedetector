<script lang="ts">
    import he from 'he';
    import dayjs from 'dayjs';
	import 'dayjs/locale/ru';
    import relativeTime from 'dayjs/plugin/relativeTime';

    dayjs.extend(relativeTime);
    const { decode } = he;
    /** @type {import('./$types').PageData} */
	export let data;
    import { page } from "$app/stores";
    import GameTable from '$lib/gameTable.svelte';
    import Layout from '../+layout.svelte';
    let steamid = $page.params.id ? $page.params.id : ""

    let calculateBy: "playtime" | "games" = "playtime";
    $: games = data.games;
    let wokePercentage = 0, slightlyWokePercentage = 0;
    $: if (games) {
        // parse info
        if (calculateBy === "games") {
            wokePercentage = games.count.woke/games.count.counted*100;
            slightlyWokePercentage = games.count.slightly_woke/games.count.counted*100;
        } else {
            wokePercentage = games.playtime.woke/games.playtime.counted*100;
            slightlyWokePercentage = games.playtime.slightly_woke/games.playtime.counted*100;
        }
        
    }

    /** @type {HTMLAnchorElement} */
    let button: HTMLAnchorElement;
</script>
<svelte:head>
    {#if data.found && games}
    <!-- Primary Meta Tags -->
    <title>насколько ПОВЕСТОЧНЫЕ игры у {data.info.name}???</title>
    <meta name="title" content="насколько ПОВЕСТОЧНЫЕ игры у {data.info.name}???" />
    <meta name="description" content="узнай, насколько библиотека у {data.info.name} повесточная, уже сегодня!!" />

    <!-- Open Graph / Facebook -->
    <meta property="og:type" content="website" />
    <meta property="og:url" content="https://wokedetector.cirnoslab.me/{steamid}" />
    <meta property="og:title" content="how WOKE are {data.info.name}'s games???" />
    <meta property="og:description" content="find out how woke {data.info.name}'s steam library is today!!" />
    <meta property="og:image" content="{data.info.avatar}" />

    <!-- Twitter -->
    <meta property="twitter:card" content="summary_large_image" />
    <meta property="twitter:url" content="https://wokedetector.cirnoslab.me/{steamid}" />
    <meta property="twitter:title" content="how WOKE are {data.info.name}'s games???" />
    <meta property="twitter:description" content="find out how woke {data.info.name}'s steam library is today!!" />
    <meta property="twitter:image" content="{data.info.avatar}" />
    {:else}
    <title>насколько твои игры ПОВЕСТОЧНЫЕ???</title>
    <meta name="title" content="насколько твои игры ПОВЕСТОЧНЫЕ???" />
    <meta name="description" content="узнай, насколько твоя библиотека повесточная, уже сегодня!!" />

    <!-- Open Graph / Facebook -->
    <meta property="og:type" content="website" />
    <meta property="og:url" content="https://wokedetector.cirnoslab.me" />
    <meta property="og:title" content="how WOKE are your games???" />
    <meta property="og:description" content="find out how woke your steam library is today with this simple tool!!" />
    <meta property="og:image" content="https://wokedetector.cirnoslab.me/favicon.png" />

    <!-- Twitter -->
    <meta property="twitter:card" content="summary_large_image" />
    <meta property="twitter:url" content="https://wokedetector.cirnoslab.me" />
    <meta property="twitter:title" content="how WOKE are your games???" />
    <meta property="twitter:description" content="find out how woke your steam library is today with this simple tool!!" />
    <meta property="twitter:image" content="https://wokedetector.cirnoslab.me/favicon.png" />
    {/if}
</svelte:head>
<div class="pad-l">
    <center>
        <h1>насколько твои игры ПОВЕСТОЧНЫЕ???</h1>
        узнай сегодня с нашим новым ДЕТЕКТОРОМ ПОВЕСТКИ!!!
        <div class="center-box">
            <form>
                <label for="steamid">steamid или ссылка на твой стим:</label>
                <input type="text" class="textbox" id="steamid" bind:value={steamid} on:keypress={(k) => k.key === "Enter" && button.click()}>
                <a href={"/" + encodeURIComponent(steamid)} bind:this={button} id="reveal" class="btn">УЗНАТЬ</a>
            </form>
            (если не знаешь, зайди на <a href="https://steamdb.info/calculator/" target="_blank">SteamDB</a> и возьми оттуда значение "SteamID")
        </div>
        
        <div>
            {#if $page.params.id}
                {#if data.found && data.info}
                    <div>
                        <img src={data.info.avatar} alt={"Аватар " + data.info.name + " в Steam"} class="avatar">
                        <span style="font-weight: bold; font-size: 1.7rem; margin-left: 0.5rem; vertical-align: middle;">Профиль {decode(data.info.name)}</span>
                    </div>
                    {#if games}
                        {#if games.count.counted > 0}
                            <div style="margin-top: 0.5rem;">
                                Рассчитывать повестку по:<br>
                                <label>
                                    <input type="radio" name="calculateBy" value="playtime" bind:group={calculateBy}> времени в игре
                                </label>
                                <label>
                                    <input type="radio" name="calculateBy" value="games" bind:group={calculateBy}> количеству игр
                                </label>
                            </div>
                            
                            <h2>Результат: 
                                {#if wokePercentage > 65 || wokePercentage + slightlyWokePercentage > 75}
                                    <span style="color: #ff0000">ВСЁ В ПОВЕСТКЕ!!!!!</span>
                                {:else if wokePercentage > 40 || wokePercentage + slightlyWokePercentage > 50}
                                    <span style="color: #e0c600">НЕМНОЖКО ПОВЕСТОЧКИ...</span>
                                {:else}
                                    <span style="color: #00ff00">ПОВЕСТКИ НЕТ!!</span>
                                {/if}<br>
                            </h2>
                            <div class="bar">
                                <div class="woke tooltip" class:left-edge={wokePercentage > 0} style:width="{wokePercentage}%">
                                    <div class="target">Явно: {wokePercentage.toFixed(2)}%</div>
                                </div>
                                <div class="slightly tooltip" class:left-edge={wokePercentage === 0} class:right-edge={wokePercentage + slightlyWokePercentage >= 100} style:width="{slightlyWokePercentage}%">
                                    <div class="target">Частично: {slightlyWokePercentage.toFixed(2)}%</div>
                                </div>
                                <div class="notwoke tooltip right-edge" class:left-edge={wokePercentage + slightlyWokePercentage === 0} style:width="{100 - (wokePercentage + slightlyWokePercentage)}%">
                                    <div class="target">Без повестки: {(100 - (wokePercentage + slightlyWokePercentage)).toFixed(2)}%</div>
                                </div>
                            </div>
                        <footer>(нажми или наведись, чтобы увидеть долю в процентах)</footer>
                        {:else}
                            мы обнаружили в твоей библиотеке игры, но рассчитать их не можем.
                        {/if}
                    {:else}
                        извини, мы не смогли получить список игр. проверь настройки приватности.<br>(доступ к игровой информации должен быть открыт)
                    {/if}
                {:else}
                    {#if data.error}
                        не удалось выполнить поиск по личной ссылке. попробуй ввести Steam64 ID.
                    {:else}
                        игрок не найден! перепроверь SteamID
                    {/if}
                {/if}
            {/if}
        </div>
    </center>
    {#if games}
    <div>
        <GameTable paginate={false} games={games.list} showPlaytime let:all let:filtered>
            <h2>Список игр (всего в списке: {all}{#if all !== filtered}, результатов: {filtered}{/if})</h2>
            {#if calculateBy === "games"}
                <footer style="margin-bottom: 0.5rem;">(учитывается {games.count.counted} из {games.count.all} игр, что составляет {(games.count.counted/games.count.all*100).toFixed(2)}%)</footer>
            {:else}
                <footer style="margin-bottom: 0.5rem;">(учитывается {(games.playtime.counted/60).toFixed(1)} ч из {(games.playtime.all/60).toFixed(1)} ч, что составляет {(games.playtime.counted/games.playtime.all*100).toFixed(2)}%)</footer>
            {/if}
        </GameTable>
    </div>
    {/if}
</div>

<footer>
    в последний раз список обновлялся {dayjs(data.lastUpdate).toDate().toLocaleString()} ({dayjs(data.lastUpdate).locale('ru').fromNow()}), полный список можно посмотреть <a href="/full-list">здесь</a><br>
    *этот список составлен по данным группы woke content detector в steam. я с ней <span style="color: red; font-weight: bold">НЕ</span> связан и совершаемые ею высказывания не одобряю. 
    <span style="color: black; font-weight: bold">данный сайт создан в юмористических целях.</span><br>
	**хотя предыдущее* заявление сделал <a href="https://cirnoslab.me/">оригинальный разработчик</a> сайта, <a href="https://github.com/The518thGuy">я</a>, будучи его переводчиком, полностью его поддерживаю.
</footer>