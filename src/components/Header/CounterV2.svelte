<script>
	import { onMount, onDestroy } from 'svelte';

	export let start = 0;
	export let end = 41349;
	export let duration = 5000; // in milliseconds
	export let text = 'повітряних тривог було оголошено в Україні з 24 лютого 2022 року';
	export let textDelay = 1000; // Delay for text appearance in milliseconds

	let count = start;
	let interval;
	let step;
	let mounted = false;
	let showText = false;

	const updateCount = () => {
		step = (end - start) / (duration / 50); // Calculate step based on duration
		interval = setInterval(() => {
			count += step;
			if ((step > 0 && count >= end) || (step < 0 && count <= end)) {
				count = end;
				clearInterval(interval);
			}
		}, 50);
	};

	onMount(() => {
		mounted = true;
		updateCount();
		setTimeout(() => {
			showText = true;
		}, textDelay);
	});

	onDestroy(() => {
		clearInterval(interval);
	});
</script>

<div class="counter">
	{#if mounted}
		<div class="value">
			<p>{Math.round(count)}</p>
		</div>

		<p class="text-opening" style:opacity={showText ? 1 : 0}>{@html text}</p>
	{/if}
</div>

<style>
	.counter {
		width: 100%;
		text-align: center;
		display: flex;
		flex-direction: column;
		align-items: center;
		color: #fff;
		font-family: 'Unbounded', sans-serif;
	}

	.value {
		width: fit-content;
		font-weight: 700;
		font-size: 8rem;
		transition: all 1s;
	}

	p {
		margin: 0;
	}

	.text-opening {
		font-size: 2rem;
		display: block;
		max-width: 700px;
		width: 100%;
		transition: all 1s;
	}

	@media (max-width: 640px) {
		.value {
			font-size: 5rem;
		}
		.text-opening {
			font-size: 1.6rem;
		}
	}

	@media (max-width: 440px) {
		.value {
			font-size: 4rem;
		}
		.text-opening {
			font-size: 1.3rem;
		}
	}
</style>
