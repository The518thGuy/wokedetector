<script lang="ts">
    import dayjs from 'dayjs';
	import 'dayjs/locale/ru';
    import relativeTime from 'dayjs/plugin/relativeTime';
    import GameTable from '$lib/gameTable.svelte';

    dayjs.extend(relativeTime);

    /** @type {import('./$types').PageData} */
	export let data;
</script>
<svelte:head>
    <title>Официальный рейтинг ПОВЕСТОЧНЫХ игр</title>
    <meta name="title" content="Официальный список ПОВЕСТОЧНЫХ игр" />
    <meta name="description" content="самый полный список ПОВЕСТОЧНЫХ игр!!!" />

    <!-- Open Graph / Facebook -->
    <meta property="og:type" content="website" />
    <meta property="og:url" content="https://wokedetector.cirnoslab.me/full-list" />
    <meta property="og:title" content="The official WOKE games ranking" />
    <meta property="og:description" content="the ultimate list of WOKE games!!!" />
    <meta property="og:image" content="https://wokedetector.cirnoslab.me/favicon.png" />

    <!-- Twitter -->
    <meta property="twitter:card" content="summary_large_image" />
    <meta property="twitter:url" content="https://wokedetector.cirnoslab.me/full-list" />
    <meta property="twitter:title" content="The official WOKE games ranking" />
    <meta property="twitter:description" content="the ultimate list of WOKE games!!!" />
    <meta property="twitter:image" content="https://wokedetector.cirnoslab.me/favicon.png" />
</svelte:head>
<div class="pad-l">
    <center>
        <h1 style="margin-bottom: 0.2rem">Рейтинг повестки</h1>
        (по данным группы woke content detector в steam)<br><br>
        <footer>нажми <a href="/">здесь</a>, чтобы узнать статистику по своему профилю<br>
        csv-файл доступен <a data-sveltekit-reload href="/data.csv">здесь</a>, последнее обновление {dayjs(data.lastUpdate).locale('ru').fromNow()}</footer>
    </center>
    <div>
        <GameTable games={data.games} let:all let:filtered>
            <h2>Список игр (всего в списке: {all}{#if all !== filtered}, результатов: {filtered}{/if})</h2>
        </GameTable>
    </div>
</div>

<footer>
    в последний раз список обновлялся {dayjs(data.lastUpdate).toDate().toLocaleString()} ({dayjs(data.lastUpdate).locale('ru').fromNow()}), полный список можно посмотреть <a href="/full-list">здесь</a><br>
    *этот список составлен по данным группы woke content detector в steam. я с ней <span style="color: red; font-weight: bold">НЕ</span> связан и совершаемые ею высказывания не одобряю. 
    <span style="color: black; font-weight: bold">данный сайт создан в юмористических целях.</span><br>
	**хотя предыдущее* заявление сделал <a href="https://cirnoslab.me/">оригинальный разработчик</a> сайта, <a href="https://github.com/The518thGuy">я</a>, будучи его переводчиком, полностью его поддерживаю.
</footer>

