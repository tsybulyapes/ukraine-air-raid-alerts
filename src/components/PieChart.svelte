<script>
	import Pie from './Pie.svelte';
	import { pie, groups } from 'd3';
	import csv from './data/official_data_uk-raw.csv';
	import { regions } from './store';

	export let lang = 'ua';
	export let title = 'Співвідношення часу з тривогою та без';
	export let subTitle = '10 областей з найбільшою кількістю тривог';

	let activeCircle = null;
	let positionX = 0;
	let positionY = 0;

	let width = 200;
	let height = 200;

	let legend = [
		{
			name: {
				ua: 'З тривогою',
				en: 'With air raid alert'
			},
			fill: '#E30101'
		},
		{
			name: {
				ua: 'Без тривоги',
				en: 'Without air raid alert'
			},
			fill: '#4D0000'
		}
	];

	$: oblData = csv.filter((row) => {
		return row.level === 'oblast';
	});

	const currTime = new Date();
	const warStartTime = new Date(2022, 1, 24, 3, 40, 0, 0);
	const warDuration = currTime.getTime() - warStartTime.getTime();

	let arcsData = [];
	$: {
		let mapData = [];

		groups(oblData, (d) => d.oblast).forEach((el) => {
			const values = el[1].map((row) => {
				const start = new Date(row.started_at);
				const end = new Date(row.finished_at);
				const duration = end - start;

				return {
					...row,
					duration
				};
			});

			const timeWithAlarm = values.reduce((acc, currVal) => acc + currVal.duration, 0);
			const timeWithoutAlarm = warDuration - timeWithAlarm;

			mapData.push({
				name: el[0],
				values: values,
				timeWithAlarm,
				timeWithoutAlarm
			});
		});

		mapData = mapData
			.filter((el) => el.name !== 'Луганська область')
			.sort((a, b) => b.timeWithAlarm - a.timeWithAlarm)
			.slice(0, 10);

		mapData = mapData.map((el) => {
			const { name, values, timeWithAlarm, timeWithoutAlarm } = el;

			const data = [
				{ name: 'with', value: timeWithAlarm, fill: '#E30101' },
				{ name: 'without', value: timeWithoutAlarm, fill: '#4D0000' }
			];

			const fullTime = timeWithAlarm + timeWithoutAlarm;
			const percentage = parseFloat(timeWithAlarm / (fullTime / 100)).toFixed(2);

			return {
				name,
				values,
				data,
				percentage
			};
		});

		mapData.forEach((el) =>
			arcsData.push({
				name: lang != 'ua' ? regions[el.name] : el.name,
				percentage: el.percentage,
				pieChartData: pieChart(el.data)
			})
		);
	}

	function parseDuration(alertTime, isFullFormat) {
		const durationMs = alertTime;
		let durationSeconds = Math.floor(durationMs / 1000);

		const years = Math.floor(durationSeconds / (60 * 60 * 24 * 365));
		durationSeconds %= 60 * 60 * 24 * 365;

		const days = Math.floor(durationSeconds / (60 * 60 * 24));
		durationSeconds %= 60 * 60 * 24;

		const hours = Math.floor(durationSeconds / (60 * 60));
		durationSeconds %= 60 * 60;

		const minutes = Math.floor(durationSeconds / 60);
		durationSeconds %= 60;

		const fullRes = `Років:${years} Днів:${days} Годин: ${hours}, хвилин: ${minutes}`;
		const shortRes = `${hours}`;

		return isFullFormat ? fullRes : shortRes;
	}

	function trackTooltip(event) {
		positionX = event.offsetX;
		positionY = event.offsetY;
	}

	const pieChart = pie()
		.sort(null)
		.value((d) => d.value);
</script>

<div class="wrap">
	<div class="head">
		<h1>{title}</h1>
		<p class="date">{subTitle}</p>
	</div>
	<!-- svelte-ignore a11y-no-static-element-interactions -->
	<div class="main" on:mousemove={trackTooltip}>
		<div class="chart">
			{#each arcsData as arcs}
				<Pie
					{width}
					{height}
					{arcs}
					on:mousemove={() => {
						activeCircle = arcs;
					}}
					on:mouseleave={() => {
						activeCircle = null;
					}}
				/>
			{/each}
			<div class="legend">
				<ul>
					{#each legend as l}
						<li>
							<span style={`background: ${l.fill}`}></span>{lang !== 'ua' ? l.name.en : l.name.ua}
						</li>
					{/each}
				</ul>
			</div>
		</div>
	</div>
</div>

<style>
	.wrap {
		font-family: 'Unbounded', sans-serif;
		margin-top: 10rem;
		font-size: 0.85rem;
		font-family: 'Unbounded', sans-serif;
		color: white;
	}

	.head {
		max-width: 900px;
		width: 100%;
		margin: 0 auto;
		color: #fff;
	}

	.date {
		font-weight: 300;
		font-size: 1rem;
		margin-top: -1%;
	}

	.main {
		padding: 40px 60px;
		height: fit-content;
		color: white;
		font-size: 0.85rem;
		font-weight: 300;
	}

	.chart {
		display: grid;
		justify-content: space-between;
		grid-template-columns: repeat(auto-fill, 200px);
		grid-gap: 2em;
		align-items: center;
	}

	@media (max-width: 700px) {
		.main {
			padding: 40px 60px;
		}
		.chart {
			grid-template-columns: repeat(auto-fill, 170px);
		}
	}

	@media (max-width: 512px) {
		.chart {
			justify-content: center;
		}
	}

	.legend {
		display: flex;
		justify-content: center;
		margin: 16px 0;
	}

	.legend ul {
		width: fit-content;
		margin: 0;
		padding: 0;
	}

	.legend ul li {
		list-style: none;
		display: flex;
		align-items: center;
		margin: 5px 0;
	}

	.legend ul li span {
		width: 15px;
		height: 15px;
		margin-right: 10px;
		border-radius: 50%;
	}
</style>
