<script>
	import { scaleLinear, extent, groups } from 'd3';
	import csv from './data/official_data_uk-raw.csv';
	import { regions } from './store';
	import viewport from './tools/useViewportAction';

	export let lang = 'ua';
	export let title = '10 найдовших тривог (окрім Луганської області)';

	let width = 1000;
	let height = 900;

	const options = {
		threshold: [0.5] // half height of the viewport
	};

	let margin = {
		top: 40,
		left: 5,
		right: 5,
		bottom: 0
	};

	let activeBar = null;
	let positionX = 0;
	let positionY = 0;
	let showBar = false;

	$: w = width - margin.right;
	$: h = height - margin.top - margin.bottom;

	$: oblData = csv.filter((row) => {
		return row.level === 'oblast';
	});

	let mapData = [];
	$: {
		groups(oblData, (d) => d.oblast).forEach((el) => {
			let maxDuration = 0;

			el[1].forEach((row) => {
				const start = new Date(row.started_at);
				const end = new Date(row.finished_at);
				const duration = end - start;

				maxDuration = Math.max(maxDuration, duration);
			});

			mapData.push({
				name: el[0],
				values: el[1],
				maxDuration
			});
		});

		mapData = mapData
			.filter((el) => el.name !== 'Луганська область')
			.sort((a, b) => b.maxDuration - a.maxDuration)
			.slice(0, 10);
	}

	$: xScale = scaleLinear()
		.domain([0, extent(mapData, (d) => d.maxDuration)[1]])
		.range([margin.left, w]);

	$: yScale = scaleLinear().domain([0, mapData.length]).range([margin.top, h]);

	$: barH = h / mapData.length - 30;

	function parseDuration(alertTime, isFullFormat) {
		const durationMs = alertTime;
		let durationSeconds = Math.floor(durationMs / 1000);

		const hours = Math.floor(durationSeconds / (60 * 60));
		durationSeconds %= 60 * 60;

		const minutes = Math.floor(durationSeconds / 60);
		durationSeconds %= 60;

		const fullUaRes = `Годин: ${hours}, хвилин: ${minutes}`;
		const fullEnRes = `Hours: ${hours}, minutes: ${minutes}`;
		const shortRes = `${hours}`;

		return isFullFormat ? (lang !== 'ua' ? fullEnRes : fullUaRes) : shortRes;
	}

	function trackTooltip(event) {
		positionX = event.offsetX;
		positionY = event.offsetY;
	}
</script>

<!-- svelte-ignore a11y-no-static-element-interactions -->
<div class="main">
	<div class="head">
		<h1>{title}</h1>
		<p class="date">24.02.2022 - 18.06.2024</p>
	</div>
	<div
		class="bar-chart"
		bind:clientWidth={width}
		bind:clientHeight={height}
		on:mousemove={trackTooltip}
		use:viewport={options}
		on:enterViewport={() => (showBar = true)}
	>
		<svg {width} {height}>
			<g>
				{#each xScale.ticks(width < 800 ? 10 : 20) as tick, index}
					<line
						x1={xScale(tick)}
						y1={margin.top}
						x2={xScale(tick)}
						y2={h}
						stroke={'white'}
						stroke-width={0.2}
					/>

					<text x={xScale(tick)} y={h + 15} text-anchor="middle" fill="white">
						{parseDuration(tick, false)}
					</text>
				{/each}
			</g>

			<g>
				{#each mapData as d, index}
					<rect
						x={margin.left}
						y={yScale(index)}
						width={showBar ? xScale(d.maxDuration) - xScale(0) : 1}
						height={barH}
						fill={'#E30101'}
						on:mousemove={() => {
							activeBar = d;
						}}
						on:mouseleave={() => {
							activeBar = null;
						}}
					/>

					<text x={margin.left} y={yScale(index) - 5} fill="white">
						{lang != 'ua' ? regions[d.name] : d.name}
					</text>
				{/each}
			</g>
		</svg>

		{#if activeBar}
			<div class="tooltip" style={`top: ${positionY + 10}px; left: ${positionX + 10}px`}>
				<p class="title">
					{#if lang !== 'ua'}
						Duration:
					{:else}
						Тривалість:
					{/if}
				</p>
				<p class="value">
					{#if lang !== 'ua'}
						{parseDuration(activeBar.maxDuration, true)}
					{:else}
						{parseDuration(activeBar.maxDuration, true)}
					{/if}
				</p>
			</div>
		{/if}
	</div>
</div>

<style>
	.head {
		max-width: 900px;
		width: 100%;
		margin: 0 auto;
	}

	.date {
		font-weight: 300;
		font-size: 1rem;
		margin-top: -1%;
	}

	.main {
		margin-top: 10rem;
		width: 100%;
		height: fit-content;
		background-color: black;
		font-size: 0.85rem;
		font-family: 'Unbounded', sans-serif;
		color: white;
	}

	svg {
		background-color: black;
	}

	rect {
		opacity: 0.5;
		transition: all 1s;
	}

	rect:hover {
		stroke: rgb(255, 255, 255, 1);
		stroke-width: 1.5;
	}

	.tooltip {
		min-width: 20px;
		width: 200px;
		position: absolute;
		background-color: rgba(0, 0, 0, 0.5);
		padding: 10px;
		border-radius: 5px;
		opacity: 1;
		color: white;
	}

	.title {
		font-weight: 700;
		color: white;
	}

	.value {
		font-weight: 300;
		color: white;
	}

	.bar-chart {
		height: 80vh;
	}
</style>
