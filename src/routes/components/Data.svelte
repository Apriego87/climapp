<script>
	import { numero } from '../stores/store.js';

	let valor;

	numero.subscribe((value) => {
		valor = value;
	});

	console.log(valor.lat);

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
		Math.round(valor.daily[0].temp.max) + 'º' + ' / ' + Math.round(valor.daily[0].temp.min) + 'º';
	let minMaxTom =
		Math.round(valor.daily[1].temp.max) + 'º' + ' / ' + Math.round(valor.daily[1].temp.min) + 'º';
	let minMaxSig =
		Math.round(valor.daily[2].temp.max) + 'º' + ' / ' + Math.round(valor.daily[2].temp.min) + 'º';

	let linkImage = `https://openweathermap.org/img/wn/${valor.current.weather[0].icon}@2x.png`;

	export let detailed;
</script>

<main>
	<div class="grid place-items-center grid-cols-2">
		<div class="variant-soft rounded-full">
			<img id="iconoTiempo" src={linkImage} alt="" />
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
	<button type="button" class="btn variant-filled" on:click={() => (detailed = true)}>Detallado</button>
</main>

<style>
	.resume {
		display: grid;
		grid-template-columns: repeat(2, 1fr);
	}

	.temp {
		display: grid;
		place-items: end;
	}

	main {
		width: 100%;
        display: grid;
        place-items: center;
	}
</style>
