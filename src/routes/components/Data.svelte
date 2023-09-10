<script>
	// importar la variable para almacenar los datos en el store
	import { numero } from '../stores/store.js';

	// exportar las variables que se pasarán a los componentes
	export let componente;
	export let params;

	// formateo de la variable para almacenar los datos
	let valor;
	numero.subscribe((value) => {
		valor = value;
	});

	// función para traducir de inglés a español un string introducido por parámetro
	function translateDescription(description) {
		// pedir datos al servidor web por petición XML HTTP
		var xhttp = new XMLHttpRequest();
		xhttp.open(
			'GET',
			'https://translate.googleapis.com/translate_a/single?client=gtx&sl=en&tl=es&dt=t&q=' +
				description,
			false
		);
		xhttp.send();

		// parsear respuesta a JSON
		var response = JSON.parse(xhttp.responseText);
		return response[0][0][0];
	}

	// función para poner en mayúscula la primera letra de un string introducido por parámetro
	function capitalizeFirstLetter(string) {
		return string.charAt(0).toUpperCase() + string.slice(1);
	}

	// función para convertir de UNIX time a día (texto). recibe como parámetro el número de día en el objeto de datos del clima (valor.daily)
	function getDayName(dia) {
		// multiplicar por 1000 para convertir de segundos a milisegundos
		let fechaMilisecs = new Date(valor.daily[dia].dt * 1000);

		// creamos una fecha y la convertimos a string
		let fecha =
			'' + (fechaMilisecs.getMonth() + 1) + '/' + fechaMilisecs.getDate() + '/' + fechaMilisecs.getFullYear();

		// pasamos la fecha anteriormente creada a formato Date
		let date = new Date(fecha);

		// devolvemos el día en texto (largo) desde la fecha en formato Date
		return date.toLocaleDateString('es-ES', { weekday: 'long' });
	}
</script>

<main>
	<!-- div que contiene icono, temperatura y tiempo actuales -->
	<div class="grid place-items-center grid-cols-2">
		<div class="variant-soft rounded-full">
			<img
				id="iconoTiempo"
				src={`https://openweathermap.org/img/wn/${valor.current.weather[0].icon}@2x.png`}
				alt=""
			/>
		</div>
		<div class="grid place-items-center">
			<div class="grid">
				<p class="text-4xl">{Math.round(valor.current.temp) + 'º'}</p>
			</div>
			<p class="text-lg">
				{capitalizeFirstLetter(translateDescription(valor.current.weather[0].main))}
			</p>
		</div>
	</div>

	<!-- div que contendrá el contenedor de los 8 días próximos -->

	<!-- las etiquetas "svelte-ignore" son para ignorar ciertos warnings, y que no salga subrayado de amarillo -->

	<div id="contenedor" class="grid w-full text-xl gap-2 p-4 place-self-start">
		<!-- svelte-ignore a11y-click-events-have-key-events -->
		<!-- svelte-ignore a11y-no-static-element-interactions -->
		<div id="contenedor2" class="grid w-full text-xl">
			<!-- svelte-ignore a11y-click-events-have-key-events -->
			<!-- svelte-ignore a11y-no-static-element-interactions -->

			<!-- los parámetros que se pasan a los componentes son: el día a mostrar (texto), la imagen del tiempo que se mostrará, y el día que corresponde en la propiedad "daily" del objeto -->

			<!-- resumen de hoy -->
			<div
				class="resume variant-soft rounded-full cursor-pointer"
				on:click={() => (
					(componente = 'dayDetailed'),
					(params = [
						'Hoy',
						`https://openweathermap.org/img/wn/${valor.daily[0].weather[0].icon}@2x.png`,
						0
					])
				)}
			>
				<div class="place-self-center">
					<p>Hoy</p>
				</div>

				<!-- div de la imagen -->
				<div class="grid place-items-center">
					<!-- svelte-ignore a11y-missing-attribute -->
					<img
						class="imageSide"
						src={`https://openweathermap.org/img/wn/${valor.daily[0].weather[0].icon}@2x.png`}
					/>
				</div>

				<!-- este div muestra la temperatura máxima y la mínima del día -->
				<div class="temp">
					<p>
						{Math.round(valor.daily[0].temp.max) +
							'º' +
							'/' +
							Math.round(valor.daily[0].temp.min) +
							'º'}
					</p>
				</div>
			</div>
			<!-- svelte-ignore a11y-click-events-have-key-events -->
			<!-- svelte-ignore a11y-no-static-element-interactions -->

			<!-- div de mañana -->
			<div
				class="resume variant-soft rounded-full cursor-pointer"
				on:click={() => (
					(componente = 'dayDetailed'),
					(params = [
						'Mañana',
						`https://openweathermap.org/img/wn/${valor.daily[1].weather[0].icon}@2x.png`,
						1
					])
				)}
			>
				<div class="place-self-center">
					<p>Mañana</p>
				</div>
				<div class="grid place-items-center">
					<!-- svelte-ignore a11y-missing-attribute -->
					<img
						class="imageSide"
						src={`https://openweathermap.org/img/wn/${valor.daily[1].weather[0].icon}@2x.png`}
					/>
				</div>
				<div class="temp">
					<p>
						{Math.round(valor.daily[1].temp.max) +
							'º' +
							'/' +
							Math.round(valor.daily[1].temp.min) +
							'º'}
					</p>
				</div>
			</div>
			<!-- svelte-ignore a11y-no-static-element-interactions -->
			<!-- svelte-ignore a11y-no-static-element-interactions -->

			<!-- bucle "for" para los días 3 a 8 -->
			{#each { length: 6 } as _, i}
				<!-- svelte-ignore a11y-no-static-element-interactions -->
				<div
					class="resume variant-soft rounded-full cursor-pointer"
					on:click={() => (
						(componente = 'dayDetailed'),
						(params = [
							capitalizeFirstLetter(getDayName(i + 2)),
							`https://openweathermap.org/img/wn/${valor.daily[i + 2].weather[0].icon}@2x.png`,
							i + 2
						])
					)}
				>
					<div class="place-self-center">
						<p>{capitalizeFirstLetter(getDayName(i + 2))}</p>
					</div>
					<div class="grid place-items-center">
						<!-- svelte-ignore a11y-missing-attribute -->
						<img
							class="imageSide"
							src={`https://openweathermap.org/img/wn/${valor.daily[i + 2].weather[0].icon}@2x.png`}
						/>
					</div>
					<div class="temp">
						<p>
							{Math.round(valor.daily[i + 2].temp.max) +
								'º' +
								'/' +
								Math.round(valor.daily[i + 2].temp.min) +
								'º'}
						</p>
					</div>
				</div>
			{/each}
		</div>
	</div>

	<!-- botón para cambiar la vista a la del tiempo las próximas 24h -->
	<button type="button" class="btn variant-filled" on:click={() => (componente = 'hourly')}
		>Próximas 24h</button
	>
</main>

<style>
	.resume {
		display: grid;
		grid-template-columns: 1fr 0.5fr 1fr;
		height: auto;
		margin: 0.25em 0;
	}

	.temp {
		display: grid;
		place-items: center;
	}

	main {
		width: 100%;
		display: grid;
		place-items: center;
	}

	.imageSide {
		width: 100%;
		max-width: 100%;
		height: auto;
	}

	#contenedor2 {
		height: 100%;
		max-height: 100%;
	}

	#contenedor {
		height: 13.5em;
		overflow-y: auto;
		margin: 1em 0;
	}
</style>
