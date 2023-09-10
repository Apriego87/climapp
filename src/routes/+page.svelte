<script type="js">
	// importar componentes
	import Alert from './components/Alert.svelte';
	import Resume from './components/Resume.svelte';

	// variables que luego se pasan a los componentes
	let visible = false;
	let componente;
	let params;
</script>

<main>
	<!-- contenedor de la alerta y el buscador -->
	<div id="cont" class="grid p-4 place-items-center max-w-full h-screen">
		<!-- alerta -->
		<div id="alert">
			<Alert bind:visible />
		</div>
		<!-- buscador -->
		<div id="busc">
			<Resume bind:visible bind:componente bind:params />
		</div>
	</div>
</main>

<style>
	@import url('https://fonts.googleapis.com/css2?family=Quicksand&display=swap');

	#alert {
		position: absolute;
		top: 0;
		width: auto;
		height: 50%;
		display: grid;
		place-items: center;
	}
</style>
