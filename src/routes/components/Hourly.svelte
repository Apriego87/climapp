<script>
	// importar la variable para almacenar los datos en el store y la variable que indica qué componente debe mostrarse
	import { numero } from '../stores/store.js';
	export let componente;

	// arrays que almacenarán las temperaturas, imágenes correspondientes, y horas a mostrar
	let arrayTemps = [];
	let arrayImages = [];
	let arrayHoras = [];

	// formateo de la variable para almacenar los datos
	let valor;
	numero.subscribe((value) => {
		valor = value;
	});

	// rellenar el array de temperaturas
	for (let i = 0; i <= 23; i++) {
		arrayTemps.push(Math.floor(valor.hourly[i].temp));
	}

	// rellenar el array de imágenes
	for (let i = 0; i <= 23; i++) {
		arrayImages.push(valor.hourly[i].weather[0].icon);
	}

	// rellenar el array de horas
	for (let i = 0; i <= 23; i++) {
		let horaUnix = valor.hourly[i].dt;

		let date = new Date(horaUnix * 1000);
		arrayHoras.push(date.getHours());
	}
</script>

<main class="grid place-items-center gap-2">
	<!-- div que contendrá las temperaturas, imágenes y horas -->
	<div id="cont">
		<!-- bucle "for" para mostrar la info. se repite por las próximas 24h -->
		{#each arrayTemps as temp, index}
			<div class="temper m-4 text-center grid gap-2 place-items-center">
				<!-- aquí se muestra la hora -->
				{arrayHoras[index] + 'h'}

				<!-- aquí se muestra la imagen -->
				<div id="img" class="variant-soft rounded-full">
					<img
						src={`https://openweathermap.org/img/wn/${arrayImages[index]}@2x.png`}
						alt="coño gordo"
					/>
				</div>

				<!-- aquí se muestra la temperatura -->
				{temp + 'ºC'}
			</div>
		{/each}
	</div>
</main>
<!-- botón para volver al componente de resumen (está fuera para que no salga en medio, habría que deslizar para verlo. POSIBLE FIX) -->
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

	#img {
		width: 75%;
		height: auto;
		max-width: 100px;
	}

	.temper {
		width: 10vw;
	}
</style>
