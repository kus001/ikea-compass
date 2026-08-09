<script lang="ts">
	import { page } from '$app/state';

	import blahaj from '$lib/assets/blahaj.png';
	import { calculateDistance, calculateBearing } from '$lib/math.ts';

	let rotation = $state(0);

	let lnglat: [number, number] = [
		Number(page.url.searchParams.get('long')),
		Number(page.url.searchParams.get('lat'))
	];

	import data from '$lib/assets/stores.json' with { type: 'json' };

	if (navigator.geolocation) {
		navigator.geolocation.getCurrentPosition((position) => {
			let lnglat_user: [number, number] = [
				Number(position.coords.longitude),
				Number(position.coords.latitude)
			];

			let closest_location = closest_ikea(lnglat[1], lnglat[0]);

			let bearing = calculateBearing(
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

			var heading = 0;

			rotation = bearing - heading;
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
