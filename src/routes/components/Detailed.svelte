<script>
    import { numero } from '../stores/store.js'
    export let detailed;   

    let valor;

	numero.subscribe((value) => {
		valor = value
	})
</script>

<main>
    <h1>{ valor.lat }, { valor.lon }</h1>
    <button type="button" class="btn variant-filled" on:click={() => (detailed = false)}>Hola</button>
</main>