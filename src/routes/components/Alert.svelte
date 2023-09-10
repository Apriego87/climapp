<script>
	// transiciones y variable para hacer visible la alerta
	import { fade, scale } from 'svelte/transition';
	export let visible;

	// texto de la alerta
	let msg1 = 'Ésta es una aplicación para comprobar el tiempo.';
	let msg2 =
		'Simplemente introduce el nombre de la ciudad que quieras buscar, y pulsa en el icono de la derecha :)';

	// función del delay de la alerta
	setTimeout(() => {
		visible = true;
	}, 1000);
</script>

<main>
	<!-- cambiar visibilidad de la alerta -->
	{#if visible}
		<!-- div contenedor de la alerta -->
		<div id="alerta">
			<aside
				id="aside"
				class="alert variant-soft text-sm max-w-md grid place-items-center gap-4"
				in:fade
				out:scale|local
			>
				<!-- mensaje -->
				<div class="alert-message">
					<h3 class="h3">Hola!</h3>
					<p>{msg1}</p>
					<p>{msg2}</p>
				</div>
				<!-- acciones -->
				<div class="alert-actions">
					<button type="button" class="btn variant-filled" on:click={() => (visible = false)}
						>Aceptar</button
					>
				</div>
			</aside>
		</div>
	{/if}
</main>

<style>
	@import url('https://fonts.googleapis.com/css2?family=Quicksand&display=swap');

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
		max-width: 100%;
		min-width: 35vw;
	}
</style>
