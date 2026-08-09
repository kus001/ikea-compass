<script lang="ts">
	import { page } from '$app/state';
	import { onMount } from 'svelte';

	import blahaj from '$lib/assets/blahaj.png';
	import { calculateDistance, calculateBearing } from '$lib/math.ts';

	let heading = $state(0);
	let bearing = $state(0);
	let rotation = $derived(bearing - heading);

	import data from '$lib/assets/stores.json' with { type: 'json' };

	function handleOrientation(event: any) {
		if (event.webkitCompassHeading !== undefined && event.webkitCompassHeading !== null) {
			heading = Number(event.webkitCompassHeading);
		} else if (event.absolute && event.alpha !== null) {
			heading = 360 - Number(event.alpha);
		}
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

	async function handleios() {
		if (
			typeof DeviceOrientationEvent !== 'undefined' &&
			typeof DeviceOrientationEvent.requestPermission === 'function'
		) {
			try {
				const permissionState = await DeviceOrientationEvent.requestPermission();
				if (permissionState === 'granted') {
					window.addEventListener('deviceorientation', handleOrientation, true);
					document.getElementById('authButton').style.display = 'none';
				} else {
					alert('Permission denied for device orientation.');
				}
			} catch (error) {
				console.error(error);
			}
		} else {
			// Non-iOS or older devices that don't require explicit permission
			window.addEventListener('deviceorientation', handleOrientation, true);
			document.getElementById('authButton').style.display = 'none';
		}
	}
</script>

<!-- svelte-ignore a11y_missing_attribute -->
<img class="box mx-auto my-24" style:--angle="{rotation + 90}deg" src={blahaj} />

<div class="grid place-items-center">
	<button id="authButton" class="btn btn-xl" onclick={handleios}>Enable Compass</button>
</div>

<style>
	.box {
		transform: rotate(var(--angle));
		/* transition: transform 0.3s ease; */
	}
</style>
