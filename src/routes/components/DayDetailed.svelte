<script>
	import { numero } from '../stores/store.js';

	export let componente;
	export let params;

	let valor;
	numero.subscribe((value) => {
		valor = value;
	});

	let item = valor.daily[params[2]];

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
</script>

<main>
	<div id="cont" class="grid place-items-center gap-4 text-center p-4 pt-0 w-full">
		<div class="grid place-items-center gap-2">
			<img src={params[1]} alt="" class="variant-soft rounded-full">
			<h1>{params[0]}</h1>
		</div>
		<div>
			<h1>{translateDescription(item.summary)}</h1>
		</div>
		<button type="button" class="btn variant-filled" on:click={() => (componente = 'data')}
			>Volver</button
		>
	</div>
</main>

<style>

	.grid > div {
		width: 75%;
	}

	img {
		width: 25%;
	}
</style>
