<script lang="ts">
	import { page } from '$app/state';
	import { onMount } from 'svelte';

	import blahaj from '$lib/assets/blahaj.png';
	import { calculateDistance, calculateBearing } from '$lib/math.ts';

	let heading = $state(0);
	let bearing = $state(0);
	let rotation = $derived(bearing - heading);

	import data from '$lib/assets/stores.json' with { type: 'json' };

	function handleOrientation(event: DeviceOrientationEvent) {
		heading = Number(event.alpha);
	}

	onMount(() => {
		window.addEventListener('deviceorientationabsolute', handleOrientation);
	});

	if (navigator.geolocation) {
		navigator.geolocation.getCurrentPosition((position) => {
			let lnglat_user: [number, number] = [
				Number(position.coords.longitude),
				Number(position.coords.latitude)
			];

			// console.log(
			// 	Number(page.url.searchParams.get('lat')),
			// 	Number(page.url.searchParams.get('long'))
			// );

			let closest_location = closest_ikea(
				Number(page.url.searchParams.get('lat')),
				Number(page.url.searchParams.get('long'))
			);

			bearing = calculateBearing(
				lnglat_user[1],
				lnglat_user[0],
				Number(closest_location.latitude),
				Number(closest_location.longitude)
			);

			// console.log(
			// 	lnglat_user[1],
			// 	lnglat_user[0],
			// 	Number(closest_location.latitude),
			// 	Number(closest_location.longitude),
			// 	closest_location.name,
			// 	bearing
			// );
		});
	} else {
		console.log('Geolocation not supported');
	}

	function closest_ikea(lat: number, long: number) {
		let dtb = 100000;
		let closest_location = data[0];
		data.forEach((loc) => {
			let distance = calculateDistance(lat, long, Number(loc.latitude), Number(loc.longitude));
			if (distance < dtb) {
				dtb = distance;
				closest_location = loc;
			}
		});
		return closest_location;
	}
</script>

<!-- svelte-ignore a11y_missing_attribute -->
<img class="box mx-auto my-12" style:--angle="{rotation + 90}deg" src={blahaj} />

<style>
	.box {
		transform: rotate(var(--angle));
		transition: transform 0.3s ease;
	}
</style>
