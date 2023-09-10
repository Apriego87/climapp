<script>
	// importar la variable para almacenar los datos en el store y el componente desplegable
	import { numero } from '../stores/store.js';
	import { TreeView, TreeViewItem } from '@skeletonlabs/skeleton';

	/* 
		exportar las variables que se pasarán a los componentes
		la variable componente indica qué componente debe mostrarse en cada momento (datos, detallado o por horas)
		la variable params son los parámetros que se pasarán al componente del tiempo detallado
	*/
	export let componente;
	export let params;

	// formateo de la variable para almacenar los datos
	let valor;
	numero.subscribe((value) => {
		valor = value;
	});

	// variable que contiene los datos del día que se pase por parámetro (variará en función del día en el que se haya hecho click)
	let item = valor.daily[params[2]];

	// variables para definir las horas de amanecer y anochecer
	let dateAmanecer = new Date(item.sunrise * 1000);
	let amanecer = dateAmanecer.getHours() + ':' + dateAmanecer.getMinutes();

	let dateAnochecer = new Date(item.sunset * 1000);
	let anochecer = dateAnochecer.getHours() + ':' + dateAnochecer.getMinutes();

	// función para traducir de inglés a español un string introducido por parámetro
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
	<!-- div que contendrá el componente del tiempo detallado -->
	<div id="cont" class="grid place-items-center gap-4 text-center p-4 pt-0 w-full">
		<!-- div que contendrá icono, día y tiempo actuales -->
		<div class="grid place-items-center gap-2">
			<img src={params[1]} alt="" class="variant-soft rounded-full" />
			<h1>
				{params[0]} -
				{#if translateDescription(item.weather[0].main) === 'Claro'}
					Despejado
				{:else}
					{translateDescription(item.weather[0].main)}
				{/if}
			</h1>
		</div>

		<!-- div que contiene la descripción del tiempo ese día, traducida al español -->
		<div id="desc">
			<h1>{translateDescription(item.summary)}</h1>
		</div>

		<!-- div que contendrá el desplegable con los datos detallados -->
		<div id="contDatos" class="grid place-items-center">
			<TreeView>
				<TreeViewItem>
					<h1>Datos generales</h1>
					<svelte:fragment slot="children">
						<div class="grid grid-cols-2 gap-2 place-items-center variant-soft rounded-lg p-2 m-2">
							<div class="dato">
								<p>Humedad: <br /> {item.humidity}%</p>
							</div>

							<div class="dato">
								<p>Lluvia: <br /> {Math.round(item.pop * 100)}%</p>
							</div>

							<div class="dato">
								<p>Presión: <br /> {item.pressure}hPa</p>
							</div>

							<div class="dato">
								<p>Amanecer: <br /> {amanecer}</p>
							</div>

							<div class="dato">
								<p>Anochecer: <br /> {anochecer}</p>
							</div>

							<div class="dato">
								<p>Viento: <br /> {Math.round(item.wind_speed * 3.6)}km/h</p>
							</div>
						</div>
					</svelte:fragment>
				</TreeViewItem>
				<TreeViewItem>
					<h1>Temperatura</h1>
					<svelte:fragment slot="children">
						<div class="grid grid-cols-2 gap-2 place-items-center variant-soft rounded-lg p-2 m-2">
							<div class="temp">
								<p>Mañana: <br /> {Math.round(item.temp.morn)} ºC</p>
							</div>
							<div class="temp">
								<p>Día: <br /> {Math.round(item.temp.day)} ºC</p>
							</div>
							<div class="temp">
								<p>Tarde: <br /> {Math.round(item.temp.eve)} ºC</p>
							</div>
							<div class="temp">
								<p>Noche: <br /> {Math.round(item.temp.night)} ºC</p>
							</div>
							<div class="temp">
								<p>Mínima: <br /> {Math.round(item.temp.min)} ºC</p>
							</div>
							<div class="temp">
								<p>Máxima: <br /> {Math.round(item.temp.max)} ºC</p>
							</div>
						</div>
					</svelte:fragment>
				</TreeViewItem>
			</TreeView>
		</div>

		<!-- botón para volver al componente resumen -->
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

	#contDatos {
		width: 100%;
	}

	p {
		text-align: center;
	}

	.dato,
	.temp {
		padding: 0.5em;
	}

	#desc {
		width: 100%;
	}
</style>
