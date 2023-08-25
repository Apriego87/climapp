<script type="js">
	import FaMapMarkerAlt from 'svelte-icons/fa/FaMapMarkerAlt.svelte';
	import { LightSwitch } from '@skeletonlabs/skeleton';
	import { numero } from '../stores/store.js'

	import Data from './Data.svelte'
	import Detailed from './Detailed.svelte'

	export let visible;
	export let detailed;

	let valor;

	numero.subscribe((value) => {
		valor = value
	})

	
	let url = ''

	/* const [data, loading, error, get] = fetchStore(url) */


	let tiempo = '';
	let temp = '';

	let minMaxHoy = '';
	let minMaxTom = '';
	let minMaxSig = '';

	export let contenido = false;

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

		/* let url = `https://api.openweathermap.org/data/2.5/onecall?lat=${lat}&lon=${long}&units=metric&appid=19dc48a3b6452ccad728e8813e9f2a86` */

		/* const [data, loading, error, get] = fetchStore(url) */
		/* que = JSON.stringify($data, null, 2) */

		que = await res.json();

		console.log(valor)

		function actualizar() {
			numero.update(
				n => que
			)
		}

		actualizar()

		console.log(valor)

		contenido = true;

		/* estado.classList.remove('variant-soft-error');
		estado.classList.remove('mt-2');

		let icono = document.getElementById('iconoTiempo');
		let ico = valor.current.weather[0].icon;

		icono.src = `https://openweathermap.org/img/wn/${ico}@2x.png`;
		console.log(que);
		temp = Math.round(valor.current.temp) + 'º';

		minMaxHoy =
			Math.round(valor.daily[0].temp.max) + 'º' + ' / ' + Math.round(valor.daily[0].temp.min) + 'º';
		minMaxTom =
			Math.round(valor.daily[1].temp.max) + 'º' + ' / ' + Math.round(valor.daily[1].temp.min) + 'º';
		minMaxSig =
			Math.round(valor.daily[2].temp.max) + 'º' + ' / ' + Math.round(valor.daily[2].temp.min) + 'º';
		tiempo = capitalizeFirstLetter(translateDescription(valor.current.weather[0].main)); */
	}
</script>

<main>
	<div id="tarjeta" class="card p-4 variant-soft max-w-md grid place-items-center">
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
			{#if detailed}
				<Detailed bind:detailed />
			{:else}
				<Data bind:detailed />
			{/if}
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
