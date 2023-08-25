<script type="js">
	import Alert from './components/Alert.svelte';
	import Detailed from './components/Detailed.svelte';
	import Resume from './components/Resume.svelte';

	let visible = false;
	let detailed = false;
</script>

<main>
	<div id="cont" class="grid p-4 place-items-center max-w-full h-screen">
		<div id="alert">
			<Alert bind:visible />
		</div>
		<div id="busc">
			<Resume bind:visible bind:detailed />
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
