<script type="js">
	import FaMapMarkerAlt from 'svelte-icons/fa/FaMapMarkerAlt.svelte';
	import { LightSwitch } from '@skeletonlabs/skeleton';
	import { fade, scale } from 'svelte/transition';

	let tiempo = '';
	let temp = '';

	let visible = false;
	let msg1 = 'Ésta es una aplicación para comprobar el tiempo.';
	let msg2 =
		'Simplemente introduce el nombre de la ciudad que quieras buscar, y pulsa en el icono de la izquierda :)';

	setTimeout(() => {
		visible = true;
	}, 1000);

	let minMaxHoy = '';
	let minMaxTom = '';
	let minMaxSig = '';

	let contenido = false;

	let latLong = [];
	let que = '';

	let d = new Date();
	let hoy = d.getDay();
	let dias = ['Domingo', 'Lunes', 'Martes', 'Miércoles', 'Jueves', 'Viernes', 'Sábado'];

	let siguiente = dias[hoy + 2];

	if (hoy === 5) {
		siguiente = dias[0];
	} else if (hoy === 6) {
		siguiente = dias[1];
	}

	function translateDescription(description) {
		var xhttp = new XMLHttpRequest();
		xhttp.open(
			'GET',
			'https://translate.googleapis.com/translate_a/single?client=gtx&sl=en&tl=es&dt=t&q=' +
				description,
			false
		);
		xhttp.send();
		var response = JSON.parse(xhttp.responseText);
		return response[0][0][0];
	}

	function capitalizeFirstLetter(string) {
		return string.charAt(0).toUpperCase() + string.slice(1);
	}

	async function getCoord() {
		let que;
		let city = document.getElementById('ciudad');
		let estado = document.getElementById('estado');

		if (city.value === '') {
			city.classList.add('variant-soft');
			city.classList.remove('variant-soft-error');
			city.classList.remove('p-2');
			estado.innerHTML = '';
			return null;
		}

		estado.classList.remove('variant-soft-error');
		estado.classList.add('p-2');
		estado.innerHTML = 'Buscando...';
		visible = false;

		try {
			const res = await fetch(`https://api.api-ninjas.com/v1/geocoding?city=${city.value}`, {
				method: 'GET',
				headers: {
					'x-api-key': 'vvEm5QeiYX5m8W8mGjHubw==qmNSokjkXTizA1mT'
				}
			});

			que = await res.json();

			latLong[0] = que[0].latitude;
			latLong[1] = que[0].longitude;

			getClima(que[0].latitude, que[0].longitude);
			contenido = true;
		} catch (Error) {
			contenido = false;
			estado.classList.add('variant-soft-error');
			estado.classList.add('p-2');
			estado.classList.add('mt-2');
			estado.innerHTML = '<p>No se ha encontrado la ciudad.</p>';
		}
	}

	async function getClima(lat, long) {
		const res = await fetch(
			`https://api.openweathermap.org/data/2.5/onecall?lat=${lat}&lon=${long}&units=metric&appid=19dc48a3b6452ccad728e8813e9f2a86`,
			{
				method: 'GET',
				headers: {
					Accept: 'application/json'
				}
			}
		);
		estado.innerHTML = '';

		que = await res.json();

		estado.classList.remove('variant-soft-error');
		estado.classList.remove('mt-2');

		let icono = document.getElementById('iconoTiempo');
		let ico = que.current.weather[0].icon;

		icono.src = `https://openweathermap.org/img/wn/${ico}@2x.png`;
		console.log(que);
		temp = Math.round(que.current.temp) + 'º';

		minMaxHoy =
			Math.round(que.daily[0].temp.max) + 'º' + ' / ' + Math.round(que.daily[0].temp.min) + 'º';
		minMaxTom =
			Math.round(que.daily[1].temp.max) + 'º' + ' / ' + Math.round(que.daily[1].temp.min) + 'º';
		minMaxSig =
			Math.round(que.daily[2].temp.max) + 'º' + ' / ' + Math.round(que.daily[2].temp.min) + 'º';
		tiempo = capitalizeFirstLetter(translateDescription(que.current.weather[0].main));
	}
</script>

<main>
	<div id="cont" class="grid place-items-center max-w-md h-screen">
		{#if visible}
			<div id="alerta">
				<aside
					id="aside"
					class="alert variant-soft text-sm max-w-md grid place-items-center"
					in:fade
					out:scale|local
				>
					<!-- Message -->
					<div class="alert-message">
						<h3 class="h3">Hola!</h3>
						<p>{msg1}</p>
						<p>{msg2}</p>
					</div>
					<!-- Actions -->
					<div class="alert-actions">
						<button type="button" class="btn variant-filled" on:click={() => (visible = false)}
							>Que sí pesao</button
						>
					</div>
				</aside>
			</div>
		{/if}
		<div id="tarjeta" class="card p-4 variant-soft max-w-md grid place-items-center gap-10">
			<div id="header" class="header p-4">
				<div id="buscador" class="iconHeader" on:click={getCoord}>
					<FaMapMarkerAlt />
				</div>
				<h1 class="text-2xl">
					<input
						class="variant-soft w-full text-center rounded-lg"
						type="text"
						name="ciudad"
						id="ciudad"
					/>
				</h1>
				<div class="iconHeader">
					<LightSwitch />
				</div>
				<p id="label"><label class="w-full text-center rounded-xl" for="estado" id="estado" /></p>
			</div>

			{#if contenido}
				<div class="grid place-items-center grid-cols-2">
					<div class="variant-soft rounded-full">
						<img id="iconoTiempo" src="" alt="" />
					</div>
					<div class="grid place-items-center">
						<div class="grid">
							<p class="text-4xl">{temp}</p>
						</div>
						<p class="text-lg">{tiempo}</p>
					</div>
				</div>

				<div class="grid w-3/4 text-xl gap-2 p-4">
					<div class="resume">
						<div class="grid">
							<p>Hoy</p>
						</div>
						<div class="temp">
							<p>{minMaxHoy}</p>
						</div>
					</div>
					<div class="resume">
						<div>
							<p>Mañana</p>
						</div>
						<div class="temp">
							<p>{minMaxTom}</p>
						</div>
					</div>
					<div class="resume">
						<div>
							<p>{siguiente}</p>
						</div>
						<div class="temp">
							<p>{minMaxSig}</p>
						</div>
					</div>
				</div>
			{/if}
		</div>
	</div>
</main>

<style>
	@import url('https://fonts.googleapis.com/css2?family=Quicksand&display=swap');

	#header {
		display: grid;
		place-items: center;
		grid-template-columns: 1fr 2fr 1fr;
	}

	#header > div {
		height: auto;
		display: grid;
		place-items: center;
	}

	.iconHeader {
		width: 25%;
		height: 25%;
	}

	:global(img) {
		width: 100%;
		height: 100%;
	}

	.resume {
		display: grid;
		grid-template-columns: repeat(2, 1fr);
	}

	.temp {
		display: grid;
		place-items: end;
	}

	#buscador {
		cursor: pointer;
	}

	#label {
		grid-column: 1 / 4;
	}

	#tarjeta > * {
		max-width: 80vw;
	}

	#aside {
		width: 70%;
		text-align: center;
		margin-top: -10vh;
	}

	#alerta {
		display: grid;
		place-items: center;
		position: absolute;
		top: 0;
		max-width: 100%;
		min-width: 35vw;
		height: 50vh;
	}
</style>
