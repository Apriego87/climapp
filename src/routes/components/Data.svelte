<script>
	import { numero } from '../stores/store.js';

	export let componente;
	export let params;

	let valor;
	numero.subscribe((value) => {
		valor = value;
	});

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

	let temp = Math.round(valor.current.temp) + 'º';
	let tiempo = capitalizeFirstLetter(translateDescription(valor.current.weather[0].main));

	let minMaxHoy =
		Math.round(valor.daily[0].temp.max) + 'º' + '/' + Math.round(valor.daily[0].temp.min) + 'º';
	let minMaxTom =
		Math.round(valor.daily[1].temp.max) + 'º' + '/' + Math.round(valor.daily[1].temp.min) + 'º';
	/* let minMaxSig =
		; */

	let linkImage = `https://openweathermap.org/img/wn/${valor.current.weather[0].icon}@2x.png`;
	let imageHoy = `https://openweathermap.org/img/wn/${valor.daily[0].weather[0].icon}@2x.png`;
	let imageTom = `https://openweathermap.org/img/wn/${valor.daily[1].weather[0].icon}@2x.png`;
	let imageSig = `https://openweathermap.org/img/wn/${valor.daily[2].weather[0].icon}@2x.png`;

	/* in:fade={{x: -100, delay}} out:scale|local */
</script>

<main>
	<div class="grid place-items-center grid-cols-2">
		<div class="variant-soft rounded-full">
			<img id="iconoTiempo" src={`https://openweathermap.org/img/wn/${valor.current.weather[0].icon}@2x.png`} alt="" />
		</div>
		<div class="grid place-items-center">
			<div class="grid">
				<p class="text-4xl">{temp}</p>
			</div>
			<p class="text-lg">{tiempo}</p>
		</div>
	</div>

	<div id="contenedor" class="grid w-full text-xl gap-2 p-4 place-self-start">
		<div id="tumami" class="grid w-full text-xl">
			<div
				class="resume variant-soft rounded-full cursor-pointer"
				on:click={() => ((componente = 'dayDetailed'), (params = ['Hoy', imageHoy, 0]))}
			>
				<div class="place-self-center">
					<p>Hoy</p>
				</div>

				<div class="grid place-items-center">
					<img class="imageSide" src={`https://openweathermap.org/img/wn/${valor.daily[0].weather[0].icon}@2x.png`} />
				</div>

				<div class="temp">
					<p>{minMaxHoy}</p>
				</div>
			</div>
			<div
				class="resume variant-soft rounded-full cursor-pointer"
				on:click={() => ((componente = 'dayDetailed'), (params = ['Mañana', imageTom, 1]))}
			>
				<div class="place-self-center">
					<p>Mañana</p>
				</div>
				<div class="grid place-items-center">
					<img class="imageSide" src={`https://openweathermap.org/img/wn/${valor.daily[1].weather[0].icon}@2x.png`} />
				</div>
				<div class="temp">
					<p>{minMaxTom}</p>
				</div>
			</div>
			{#each { length: 6 } as _, i}
				<div
					class="resume variant-soft rounded-full  cursor-pointer"
					on:click={() => ((componente = 'dayDetailed'), (params = [dias[i + 1], `https://openweathermap.org/img/wn/${valor.daily[i+2].weather[0].icon}@2x.png`, i+2]))}
				>
					<div class="place-self-center">
						<p>{dias[i + 1]}</p>
					</div>
					<div class="grid place-items-center">
						<img class="imageSide" src={`https://openweathermap.org/img/wn/${valor.daily[i+2].weather[0].icon}@2x.png`} />
					</div>
					<div class="temp">
						<p>{Math.round(valor.daily[i+2].temp.max) + 'º' + '/' + Math.round(valor.daily[i+2].temp.min) + 'º'}</p>
					</div>
				</div>
			{/each}
		</div>

		<!--  -->
	</div>
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

	#tumami {
		height: 100%;
		max-height: 100%;
	}

	#contenedor {
		height: 13.5em;
		overflow-y: auto;
		margin: 1em 0;
	}
</style>
