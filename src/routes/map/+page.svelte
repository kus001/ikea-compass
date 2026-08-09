<script lang="ts">
	import { goto } from '$app/navigation';
	import {
		MapLibre,
		Marker,
		NavigationControl,
		ScaleControl,
		GlobeControl
	} from 'svelte-maplibre-gl';

	let lnglat: [number, number] = $state([-73.2182, 44.4735]);

	function navigate() {
		const searchParams = new URLSearchParams();
		searchParams.set('lat', String(lnglat[1]));
		searchParams.set('long', String(lnglat[0]));
		goto(`/navigate?${searchParams.toString()}`);
	}
</script>

<MapLibre
	class="m-8 h-[85vh] min-h-[300px] rounded-md"
	style="https://raw.githubusercontent.com/go2garret/maps/main/src/assets/json/openStreetMap.json"
	zoom={15}
	center={{ lng: lnglat[0], lat: lnglat[1] }}
>
	<NavigationControl />
	<ScaleControl />
	<GlobeControl />

	<Marker bind:lnglat draggable />
</MapLibre>
<button class="btn mx-8 h-[5vh] w-[calc(100%-4rem)] rounded-md btn-primary" onclick={navigate}
	>Directions</button
>
