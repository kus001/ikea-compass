<script>
	import background from '$lib/assets/background.gif';
	import { resolve } from '$app/paths';
</script>

<svelte:head>
	<title>Home</title>
	<meta name="description" content="Camgo homepage" />
</svelte:head>

<main
	class="flex items-center justify-center bg-cover text-center"
	style="background-image: url({background});"
>
	<div class="hero min-h-screen bg-base-100 opacity-90">
		<div class="hero-content text-center">
			<div class="max-w-l">
				<h1 class="mb-16 text-5xl font-bold">Welcome to Normal Maps</h1>

				<a class="btn btn-primary btn-xl" href={resolve('/map')}>Navigate</a>
			</div>
		</div>
	</div>
</main>
