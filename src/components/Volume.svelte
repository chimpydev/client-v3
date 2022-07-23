<script>
	
	import { onMount, onDestroy } from 'svelte'
	import { getVolume } from '../lib/graph'
	import { formatToDisplay } from '../lib/utils'
	import { SPINNER_ICON } from '../lib/icons'
	import { prices } from '../lib/stores'

	let v;
	let volumeFTM;
	let volumeUSD;

	onMount(async () => {
		const res = await getVolume();
		// console.log('res', res);
		volumeFTM = res.volumeFTM;
		volumeUSD = res.volumeUSD;
		v = setInterval(async () => {
			const res = await getVolume();
			volumeFTM = res.volumeFTM;
			volumeUSD = res.volumeUSD;
		}, 60*1000);
	});
	
	onDestroy(() => {
		clearInterval(v);
	});

	let volume_eth;
	let volume_usd;

	function calculateVolume(_prices, _volumeFTM, _volumeUSD) {
		// console.log('calculateVolume', _prices, _volumeFTM, _volumeUSD);
		if (!_prices['FTM-USD']) return;
		volume_eth = _volumeUSD * 1 / _prices['FTM-USD'] + _volumeFTM * 1;
		volume_usd = _volumeUSD * 1 + _prices['FTM-USD'] * _volumeFTM * 1;
		volume_usd = Math.round(volume_usd);
	}

	$: calculateVolume($prices, volumeFTM, volumeUSD);

</script>

<style>
	.loading-icon :global(svg) {
		height: 16px;
		fill: none;
	}
	.dollar-amount {
		color: var(--sonic-silver);
		font-weight: 400;
	}
</style>

{#if volume_usd}
	${formatToDisplay(volume_usd)}
{:else}
	<div class='loading-icon'>{@html SPINNER_ICON}</div>
{/if}
