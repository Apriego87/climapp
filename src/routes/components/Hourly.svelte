<script>
	import { numero } from '../stores/store.js';
	export let componente;

	let delay = 500

	/* in:fade={{x: -100, delay}} out:scale|local */

	

	let arrayTemps = [];
	let arrayImages = [];
	let arrayHoras = [];
	
	let valor;
	numero.subscribe((value) => {
		valor = value;
	});

	for (let i = 0; i <= 23; i++) {
		arrayTemps.push(Math.floor(valor.hourly[i].temp));
	}

	for (let i = 0; i <= 23; i++) {
		arrayImages.push(valor.hourly[i].weather[0].icon);
	}

	for (let i = 0; i <= 23; i++) {
		let horaUnix = valor.hourly[i].dt;

		let date = new Date(horaUnix * 1000);
		arrayHoras.push(date.getHours());
	}
</script>

<main class="grid place-items-center gap-2" >
	<div id="cont">
		{#each arrayTemps as temp, index}
			<div class="temper m-4 text-center grid gap-2">
				{arrayHoras[index] + 'h'}
				<div class="variant-soft rounded-full">
					<img
						src={`https://openweathermap.org/img/wn/${arrayImages[index]}@2x.png`}
						alt="coño gordo"
					/>
				</div>
				{temp + 'ºC'}
			</div>
		{/each}
	</div>
</main>
<button type="button" class="btn variant-filled mt-4" on:click={() => (componente = 'data')}
	>Resumen</button
>

<style>
	#cont {
		display: flex;
		flex-direction: row;
	}

	main {
		overflow: auto;
		width: 100%;
		height: auto;
	}

	img {
		width: 100%;
		height: auto;
	}

	.temper {
		width: 10vw;
	}
</style>
