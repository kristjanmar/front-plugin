<script>
	import { browser } from '$app/environment'
	import '../app.css'

	let { children } = $props()
</script>

{#if browser}
	{@render children()}
{/if}
