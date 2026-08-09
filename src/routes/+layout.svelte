<script lang="ts">
	import './layout.css';
	import favicon from '$lib/assets/favicon.ico';

	import Header from '$lib/header.svelte';

	let { children } = $props();
</script>

<svelte:head><link rel="icon" href={favicon} /></svelte:head>
<Header />
{@render children()}
