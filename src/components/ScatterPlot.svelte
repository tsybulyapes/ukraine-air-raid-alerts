<script>
	import DropdownSelector from './DropdownSelector.svelte';
	import csv from './data/official_data_uk-raw.csv';
	import { scaleLinear, extent } from 'd3';

	export let lang = 'ua';
	export let title = 'Графік тривалості повітряних тривог';
	export let subTitle = 'За кожен день повномасштабного вторгненя в залежності від часу доби';

	let showObl = 'м. Київ';

	let width = 0;
	let height = 0;

	let activeCircle = null;
	let positionX = 0;
	let positionY = 0;

	let data = [];

	let margin = {
		top: 40,
		left: 60,
		right: 10,
		bottom: 0
	};

	$: chartWidth = width - margin.right;
	$: chartHeight = height - margin.top - margin.bottom;

	csv.forEach((row) => {
		let obl = row.oblast;
		let start = new Date(row.started_at);
		let end = new Date(row.finished_at);
		let duration = end - start;

		data.push({
			name: obl,
			start: start,
			end: end,
			alertTime: duration,
			hour: parseInt(row.started_at.substring(11, 13))
		});
	});

	$: dataByObl = data.filter((row) => row.name == showObl);

	$: xScale = scaleLinear()
		.domain(extent(dataByObl, (d) => new Date(d.start)))
		.range([margin.left, chartWidth]);

	$: yScale = scaleLinear().domain([0, 24]).range([chartHeight, margin.top]);

	$: rScale = scaleLinear()
		.domain(extent(dataByObl, (d) => d.alertTime))
		.range([1, 30]);

	// $: console.log(extent(dataByObl, (d) => new Date(d.start)));

	const parseDate = (date) => {
		const year = new Date(date).getFullYear();
		const month = new Date(date).getMonth() + 1;
		return `${month}/${year}`;
	};

	function trackTooltip(event) {
		positionX = event.offsetX;
		positionY = event.offsetY;
	}

	function parseDuration(alertTime) {
		const durationMs = alertTime;
		let durationSeconds = Math.floor(durationMs / 1000);

		const hours = Math.floor(durationSeconds / (60 * 60));
		durationSeconds %= 60 * 60;

		const minutes = Math.floor(durationSeconds / 60);
		durationSeconds %= 60;

		const uaDuration = `Годин: ${hours}, хвилин: ${minutes}`;
		const enDuration = `Hours: ${hours}, minutes: ${minutes}`;

		return lang !== 'ua' ? enDuration : uaDuration;
	}

	let unicNames = {};

	csv.forEach((row) => {
		unicNames[row.oblast] = 0;
	});

	$: names = Object.keys(unicNames);

	let selected = 'м. Київ';
	$: if (selected) {
		showObl = selected;
	}
</script>

<!-- svelte-ignore a11y-no-static-element-interactions -->
<div class="wrap">
	<div class="head">
		<h1>{title}</h1>
		<p class="date">{subTitle}</p>

		<DropdownSelector
			placeholder={selected}
			dropdownItems={names}
			bind:selectedItem={selected}
			{lang}
		/>
	</div>
	<main bind:clientWidth={width} bind:clientHeight={height} on:mousemove={trackTooltip}>
		<svg {width} {height}>
			<g class="axis">
				{#each yScale.ticks(10) as tick}
					<line
						x1={margin.left - 20}
						y1={yScale(tick)}
						x2={chartWidth}
						y2={yScale(tick)}
						stroke="white"
						stroke-width={0.2}
					/>
					<text x={margin.left - 40} y={yScale(tick) + 6} text-anchor="middle" fill="white"
						>{tick}</text
					>
				{/each}
			</g>

			<g class="x-axis">
				{#each xScale.ticks(width > 800 ? 8 : 3) as tick}
					<line
						x1={xScale(tick)}
						y1={chartHeight}
						x2={xScale(tick)}
						y2={chartHeight + 10}
						stroke="white"
						stroke-width={2}
					/>
					<text x={xScale(tick)} y={chartHeight + 30} text-anchor="middle" fill="white"
						>{parseDate(tick)}</text
					>
				{/each}
			</g>

			<line
				x1={margin.left - 20}
				y1={chartHeight}
				x2={chartWidth}
				y2={chartHeight}
				stroke="white"
				stroke-width={2}
			/>

			{#each dataByObl as row}
				<circle
					cx={xScale(row.start)}
					cy={yScale(row.hour)}
					r={rScale(row.alertTime)}
					fill={'#E30101'}
					on:mousemove={() => {
						activeCircle = row;
					}}
					on:mouseleave={() => {
						activeCircle = null;
					}}
				/>
			{/each}
		</svg>

		{#if activeCircle}
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
						{parseDuration(activeCircle.alertTime)}
					{:else}
						{parseDuration(activeCircle.alertTime)}
					{/if}
				</p>
			</div>
		{/if}
	</main>
</div>

<style>
	.wrap {
		font-family: 'Unbounded', sans-serif;
		margin-top: 10rem;
		font-size: 0.85rem;
		font-family: 'Unbounded', sans-serif;
		color: white;
	}

	main {
		width: 100%;
		height: 580px;
		background-color: black;
		font-size: 0.85rem;
		font-family: 'Unbounded', sans-serif;
		color: white;
	}

	circle {
		opacity: 0.5;
		transition: all 1s;
	}

	circle:hover {
		stroke: rgb(255, 255, 255, 1);
		stroke-width: 1.5;
		transition: none;
		transition-duration: 0ms;
	}

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

	.tooltip {
		min-width: 200px;
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
	}

	.value {
		font-weight: 300;
	}
</style>
