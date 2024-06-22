<script>
	import { json, geoMercator, geoPath, groups, scaleLinear } from 'd3';
	import csv from './data/official_data_uk-raw.csv';
	import geojson from './data/ukraine-adm-regions.json';
	import viewport from './tools/useViewportAction';
	import { regions } from './store';

	export let lang = 'ua';
	export let title = 'Мапа кількості повітряних тривог';
	export let legendTitle = 'Кількість';

	let width = 0;
	let height = 0;

	let activeObl = null;
	let positionX = 0;
	let positionY = 0;

	const options = {
		threshold: [0.7] // half height of the viewport
	};

	let animate = false;

	export let colors = ['#E30101', '#4D0000'];

	const getColor = scaleLinear().domain([439, 4220]).range(colors);

	$: oblData = csv.filter((row) => {
		return row.level === 'oblast';
	});

	let mapData = {};
	$: dataByObl = groups(oblData, (d) => d.oblast).forEach((el) => {
		mapData[el[0]] = {
			values: el[1],
			total: el[1].length
		};
	});

	$: projection = geoMercator().fitSize([width, height], geojson);
	$: pathGenerator = geoPath(projection);

	let counties = [];
	$: if (geojson)
		counties = geojson.features.map((feature, index) => {
			return {
				...feature,
				path: pathGenerator(feature),
				id: index
			};
		});

	function trackTooltip(event) {
		positionX = event.offsetX - 80;
		positionY = event.offsetY;
	}
</script>

<div class="wrap">
	<div class="head">
		<h1>{title}</h1>
		<p class="date">24.02.2022 - 18.06.2024</p>
	</div>
	<main>
		<!-- svelte-ignore a11y-no-static-element-interactions -->
		<div
			class="map"
			bind:clientWidth={width}
			bind:clientHeight={height}
			on:mousemove={trackTooltip}
			use:viewport={options}
			on:enterViewport={() => (animate = true)}
		>
			<svg {width} {height}>
				{#each counties as obl}
					{@const name = obl.properties['UA_NAME']}
					<path
						d={obl.path}
						fill={animate
							? name === 'Луганська область'
								? '#4D0000'
								: mapData[name]
									? getColor(mapData[name]['total'])
									: '#8C8C8C'
							: '#111'}
						class:pulse={name === 'Луганська область'}
						on:mousemove={() => {
							activeObl = obl;
							//console.log(activeObl)
						}}
						on:mouseleave={() => {
							activeObl = null;
						}}
					/>
				{/each}
			</svg>
		</div>

		{#if activeObl && activeObl.properties.UA_NAME != 'АР Крим' && activeObl.properties.UA_NAME != 'м. Севастополь'}
			<div class="tooltip" style={`top: ${positionY + 10}px; left: ${positionX + 90}px`}>
				<p class="title">
					{lang !== 'ua' ? activeObl.properties.NAME_1 : activeObl.properties.UA_NAME}
				</p>
				<p class="value">
					{#if lang !== 'ua'}
						Total: <strong> {mapData[activeObl.properties.UA_NAME].total}</strong>
					{:else}
						К-сть тривог: <strong> {mapData[activeObl.properties.UA_NAME].total}</strong>
					{/if}
				</p>
			</div>
		{/if}

		<div class="legend">
			<p class="date">{legendTitle}</p>
			<div class="stripe"></div>
			<div class="values">
				<p>439</p>
				<p>4586</p>
			</div>
		</div>
	</main>
</div>

<style>
	.wrap {
		font-family: 'Unbounded', sans-serif;
		margin-top: 15rem;
		font-size: 0.85rem;
		color: white;
	}
	main {
		position: relative;
		background-color: black;
		width: 100%;
		font-size: 0.8rem;
		font-family: 'Unbounded', sans-serif;
		color: white;
		margin-top: 1rem;
	}

	.map {
		margin: 0 auto;
		width: 100%;
		height: 85vh;
		overflow: hidden;
	}

	@media (max-width: 640px) {
		.map {
			height: 500px;
		}
		.legend {
			bottom: 30px !important;
		}
	}

	@media (max-width: 420px) {
		.map {
			height: 320px;
		}
		.legend {
			bottom: 0px !important;
		}
	}

	path {
		stroke: black;
		stroke-width: 0.2px;
		transition: fill 1.5s ease-out;
	}

	path:hover {
		opacity: 0.8;
	}

	@keyframes pulse {
		0% {
			opacity: 0.5;
		}
		50% {
			opacity: 1;
		}
		100% {
			opacity: 0.5;
		}
	}

	.pulse {
		animation: pulse 2s ease-in-out infinite;
	}

	.tooltip {
		min-width: 200px;
		width: 200px;
		position: absolute;
		background-color: rgba(0, 0, 0, 0.5);
		padding: 10px;
		border-radius: 5px;
		opacity: 1;
	}

	.title {
		font-weight: 700;
	}

	.value {
		font-weight: 300;
	}

	.head {
		max-width: 900px;
		width: 100%;
		margin: 0 auto;
		font-size: 0.85rem;
		font-family: 'Unbounded', sans-serif;
		color: white;
		margin-top: 5rem;
	}

	.date {
		font-weight: 300;
		font-size: 1rem;
		margin-top: -1%;
	}

	.legend {
		position: absolute;
		bottom: 100px;
		left: 5%;
		width: 200px;
	}

	.stripe {
		width: 100%;
		height: 10px;
		background: linear-gradient(90deg, #e30101, #4d0000);
		border-radius: 5px;
		margin-top: -8px;
	}

	.values {
		display: flex;
		justify-content: space-between;
	}

	.values p {
		display: block;
		margin-top: 5px;
	}
</style>
