<script type="js">
	import FaMapMarkerAlt from 'svelte-icons/fa/FaMapMarkerAlt.svelte';
	import { LightSwitch } from '@skeletonlabs/skeleton';
	import fetchStore from '../fetch.js'

	export let visible;
	export let detailed;

	
	let url = ''

	const [data, loading, error, get] = fetchStore(url)


	let tiempo = '';
	let temp = '';

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
		console.log(visible)

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
		/* const res = await fetch(
			`https://api.openweathermap.org/data/2.5/onecall?lat=${lat}&lon=${long}&units=metric&appid=19dc48a3b6452ccad728e8813e9f2a86`,
			{
				method: 'GET',
				headers: {
					Accept: 'application/json'
				}
			}
		); */
		estado.innerHTML = '';

		let url = `https://api.openweathermap.org/data/2.5/onecall?lat=${lat}&lon=${long}&units=metric&appid=19dc48a3b6452ccad728e8813e9f2a86`

		/* const [data, loading, error, get] = fetchStore(url) */
		que = JSON.stringify($data, null, 2)

		/* que = await res.json(); */

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
	<div id="tarjeta" class="card p-4 variant-soft max-w-md grid place-items-center gap-10">
		<div id="header" class="header p-4">
			<div id="toggle" class="iconHeader grid place-self-center -ml-6">
				<LightSwitch />
			</div>
			<div class="">
				<h1 class="text-2xl">
					<input
						class="variant-soft w-full text-center rounded-lg"
						type="text"
						name="ciudad"
						id="ciudad"
					/>
				</h1>
			</div>
			<div id="buscador" class="iconHeader -mr-6" on:click={getCoord}>
				<FaMapMarkerAlt />
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
			<button type="button" class="btn variant-filled" on:click={() => (detailed = true)}>Hola</button>
		{/if}
	</div>
</main>

<style>
	#header {
		display: grid;
		place-items: center;
		grid-template-columns: 1fr 2fr 1fr;
	}

	#header > div {
		height: auto;
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
		width: 25%;
		height: 25%;
		min-width: 24px;
		min-height: 32px;
	}

	#ciudad {
		min-width: 10px;
	}

	#label {
		grid-column: 1 / 4;
	}

	#tarjeta > * {
		max-width: 80vw;
	}
</style>
