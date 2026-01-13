<script lang="ts">
	import favicon from '$lib/assets/favicon.svg';
    import '../app.css';
	import { env } from '$env/dynamic/public';

	let { children } = $props();

	const { PUBLIC_GOOGLE_MAPS_API_KEY } = env

	console.log('env =', PUBLIC_GOOGLE_MAPS_API_KEY)
</script>

<svelte:head>
	<script src="https://maps.googleapis.com/maps/api/js?key={PUBLIC_GOOGLE_MAPS_API_KEY}&libraries=places"></script>
	<link rel="icon" href={favicon} />
</svelte:head>

{@render children()}
