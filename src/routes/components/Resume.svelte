<script type="js">
	// imports de los iconos y la variable para almacenar los datos en el store
	import FaMapMarkerAlt from 'svelte-icons/fa/FaMapMarkerAlt.svelte';
	import { LightSwitch } from '@skeletonlabs/skeleton';
	import { numero } from '../stores/store.js';

	// imports de los componentes
	import Data from './Data.svelte';
	import Hourly from './Hourly.svelte';
	import DayDetailed from './DayDetailed.svelte';

	// variables que se pasan a los componentes
	export let visible;
	export let componente;
	export let params;

	// formateo de la variable para almacenar los datos
	let valor;
	numero.subscribe((value) => {
		valor = value;
	});

	// variable que cambiará si se ha introducido una ciudad correctamente
	export let contenido = false;

	// latLong almacenará latitud y longitud de la ciudad que busquemos
	let latLong = [];

	// variable que guarda (temporalmente) los datos recolectados
	let datos = '';

	// función para sacar las coordenadas de la ciudad introducida
	async function getCoord() {

		// para formatear el aspecto de la barra y la etiqueta de estado en caso de error
		let city = document.getElementById('ciudad');
		let estado = document.getElementById('estado');

		// si está vacío, no hacer nada
		if (city.value === '') {
			city.classList.add('variant-soft');
			city.classList.remove('variant-soft-error');
			city.classList.remove('p-2');
			estado.innerHTML = '';
			return null;
		}

		// quitar el color de error (si estuviera), indicar que está buscando, y ocultar la alerta de instrucciones
		estado.classList.remove('variant-soft-error');
		estado.classList.add('p-2');
		estado.innerHTML = 'Buscando...';
		visible = false;

		// bloque para buscar las coordenadas, y capturar error (si hubiera)
		try {
			// petición
			const res = await fetch(`https://api.api-ninjas.com/v1/geocoding?city=${city.value}`, {
				method: 'GET',
				headers: {
					'x-api-key': 'vvEm5QeiYX5m8W8mGjHubw==qmNSokjkXTizA1mT'
				}
			});

			// pasar datos a JSON
			datos = await res.json();

			// rellenar array con latitud y longitud
			latLong[0] = datos[0].latitude;
			latLong[1] = datos[0].longitude;

			// llamar a la función para obtener los datos (declarada abajo)
			getClima(datos[0].latitude, datos[0].longitude);
		} 
		// recoger error (si hubiera)
		catch (Error) {
			// no mostrar layout de datos, y añadir color y texto de error
			contenido = false;
			estado.classList.add('variant-soft-error');
			estado.classList.add('p-2');
			estado.classList.add('mt-2');
			estado.innerHTML = '<p>No se ha encontrado la ciudad.</p>';
		}
	}

	// función que obtiene datos climatológicos en base a latitud y longitud
	async function getClima(lat, long) {
		// petición de datos
		const res = await fetch(
			`https://api.openweathermap.org/data/2.8/onecall?lat=${lat}&lon=${long}&units=metric&appid=19dc48a3b6452ccad728e8813e9f2a86`,
			{
				method: 'GET',
				headers: {
					Accept: 'application/json'
				}
			}
		);

		// resetear texto de la etiqueta (si hubiera)
		estado.innerHTML = '';

		// parsear datos a JSON
		datos = await res.json();

		// pasar los datos de la variable temporal al store
		function actualizar() {
			numero.update((n) => datos);
		}

		actualizar();

		// variable que mostrará el layout de resumen
		contenido = true;
	}
</script>

<main>
	<!-- div contenedor del resumen -->
	<div id="tarjeta" class="card p-4 variant-soft max-w-md grid place-items-center">
		<!-- div con el header, que mostrará el interruptor de modo claro/oscuro, botón y barra de búsqueda -->
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

			<!-- etiqueta que mostrará el estado de la búsqueda -->
			<p id="label">
				<label class="w-full text-center rounded-xl" for="estado" id="estado" />
			</p>
		</div>

		<!-- dependiendo de si se han encontrado datos o no, se mostrarán los distintos componentes -->
		{#if contenido}
			{#if componente === 'data'}
				<Data bind:componente bind:params />
			{:else if componente === 'dayDetailed'}
				<DayDetailed bind:componente bind:params />
			{:else if componente === 'hourly'}
				<Hourly bind:componente />
			{:else}
				<Data bind:componente bind:params />
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
