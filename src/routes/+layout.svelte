<script>
	import { browser } from '$app/environment'
	import '../app.pcss'
</script>

{#if browser}
	<slot></slot>
{/if}
