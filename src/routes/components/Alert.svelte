<script>
	import { fade, scale } from 'svelte/transition';
	export let visible;

	let msg1 = 'Ésta es una aplicación para comprobar el tiempo.';
	let msg2 =
		'Simplemente introduce el nombre de la ciudad que quieras buscar, y pulsa en el icono de la izquierda :)';

	setTimeout(() => {
		visible = true;
	}, 1000);
</script>

<main>
	{#if visible}
		<div id="alerta">
			<aside
				id="aside"
				class="alert variant-soft text-sm max-w-md grid place-items-center gap-4"
				in:fade
				out:scale|local
			>
				<!-- Message -->
				<div class="alert-message">
					<h3 class="h3">Hola!</h3>
					<p>{msg1}</p>
					<p>{msg2}</p>
				</div>
				<!-- Actions -->
				<div class="alert-actions">
					<button type="button" class="btn variant-filled" on:click={() => (visible = false)}
						>Que sí pesao</button
					>
				</div>
			</aside>
		</div>
	{/if}
</main>

<style>
	@import url('https://fonts.googleapis.com/css2?family=Quicksand&display=swap');

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

	#aside {
		width: auto;
		max-width: 75%;
		height: auto;
		text-align: center;
		margin-top: -10vh;
	}

	#alerta {
		display: grid;
		place-items: center;
		/* position: absolute;
		top: 0; */
		max-width: 100%;
		min-width: 35vw;
	}
</style>
