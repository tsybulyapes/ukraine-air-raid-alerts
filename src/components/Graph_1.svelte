<script>
	import { groups, scaleLinear, extent, line, curveMonotoneX } from 'd3';
	import csv from './data/data_graph_1.csv';

	export let lang = 'ua';
	export let title = 'Графік кількості повітряних тривог';
	export let subTitle = 'За кожен рік повномасштабного вторгненя';

	const colors = {
		2022: '#E30101',
		2023: '#ff6d00',
		2024: '#ffffff'
	};

	const lineGenerator = line()
		.x((d) => d.x)
		.y((d) => d.y)
		.curve(curveMonotoneX);

	let width = 0;
	let height = 0;

	let margin = {
		top: 40,
		left: 65,
		right: 20,
		bottom: 0
	};

	$: chartWidth = width - margin.right;
	$: chartHeight = height - margin.top - margin.bottom;

	$: oblData = csv.map((el) => {
		return {
			year: parseInt(el.year),
			month: parseInt(el.month),
			strike_count: parseInt(el.strike_count)
		};
	});

	$: xScale = scaleLinear()
		.domain(extent(oblData, (d) => d.month))
		.range([margin.left, chartWidth]);

	$: yScale = scaleLinear()
		.domain(extent(oblData, (d) => d.strike_count))
		.range([chartHeight, margin.top]);

	let dataByYear = [];
	$: groups(oblData, (d) => d.year).forEach((el) => {
		dataByYear.push({
			year: el[0],
			values: el[1]
		});
	});

	$: renderedData = dataByYear.map((el) => {
		const renderedValues = el.values.map((mon) => {
			return {
				...mon,
				x: xScale(mon.month),
				y: yScale(mon.strike_count)
			};
		});

		return {
			...el,
			values: renderedValues,
			path: lineGenerator(renderedValues),
			stroke: colors[el.year],
			strokeWidth: 4.0,
			opacity: 1.0
		};
	});

	//function parseMonth(monthNum) {
	//	const monthesMap = new Map([
	//		[1, 'Січ'],
	//		[2, 'Лют'],
	//		[3, 'Бер'],
	//		[4, 'Кві'],
	//		[5, 'Тра'],
	//		[6, 'Чер'],
	//		[7, 'Лип'],
	//		[8, 'Сер'],
	//		[9, 'Вер'],
	//		[10, 'Жов'],
	//		[11, 'Лис'],
	//		[12, 'Гру']
	//	]);

	//	return monthesMap.get(monthNum);
	//}

	let monthsUa = [
		'Січ',
		'Лют',
		'Бер',
		'Кві',
		'Тра',
		'Чер',
		'Лип',
		'Сер',
		'Вер',
		'Жов',
		'Лис',
		'Гру'
	];
	let monthsEng = [
		'Jan',
		'Feb',
		'Mar',
		'Apr',
		'May',
		'Jun',
		'Jul',
		'Aug',
		'Sep',
		'Oct',
		'Nov',
		'Dec'
	];
</script>

<div class="wrap">
	<div class="head">
		<h1>{title}</h1>
		<p class="date">{subTitle}</p>
	</div>
	<!-- svelte-ignore a11y-no-static-element-interactions -->
	<div class="chart">
		<div class="main" bind:clientWidth={width} bind:clientHeight={height}>
			<svg width="100%" {height}>
				<g class="axis">
					{#each yScale.ticks(10) as tick}
						<line
							x1={margin.left - 10}
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

				<g class="axis">
					{#each xScale.ticks(width > 800 ? 12 : 4) as tick}
						<line
							x1={xScale(tick)}
							y1={chartHeight}
							x2={xScale(tick)}
							y2={chartHeight + 10}
							stroke="white"
							stroke-width={2}
						/>
						<text x={xScale(tick)} y={chartHeight + 30} text-anchor="middle" fill="white"
							>{lang != 'ua' ? monthsEng[tick - 1] : monthsUa[tick - 1]}</text
						>
					{/each}
				</g>

				{#each renderedData as { path, stroke, strokeWidth, opacity }}
					<path d={path} {stroke} stroke-width={strokeWidth} {opacity} />
				{/each}

				<line
					x1={margin.left - 5}
					y1={chartHeight}
					x2={chartWidth + 5}
					y2={chartHeight}
					stroke="white"
					stroke-width={2}
				/>
			</svg>
		</div>
		<div class="legend">
			<ul>
				<li><span style={`background: #E30101`}></span>2022</li>
				<li><span style={`background: #ff6d00`}></span>2023</li>
				<li><span style={`background: #ffffff`}></span>2024</li>
			</ul>
		</div>
	</div>
</div>

<style>
	.wrap {
		font-family: 'Unbounded', sans-serif;
		margin-top: 10rem;
		font-size: 0.85rem;
		color: white;
	}

	.head {
		max-width: 900px;
		margin: 0 auto;
		color: #fff;
	}

	.chart {
		display: grid;
		grid-template-columns: 1fr 200px;
	}

	.main {
		position: relative;
		height: 600px;
		max-width: 900px;
	}

	.date {
		font-weight: 300;
		font-size: 1rem;
		margin-top: -1%;
	}

	path {
		fill: transparent;
		stroke-linecap: round;
	}

	.legend {
		display: flex;
		align-items: center;
	}

	.legend ul {
		margin: 0;
		padding: 0;
	}

	.legend ul li {
		list-style: none;
		display: flex;
		align-items: center;
		margin: 5px 0;
		width: fit-content;
	}

	.legend ul li span {
		width: 80px;
		height: 4px;
		margin-right: 10px;
		border-radius: 3px;
		display: block;
	}

	@media (max-width: 800px) {
		.chart {
			display: block;
		}
		.legend {
			justify-content: center;
			margin-top: 20px;
		}
		.legend ul {
			display: flex;
			justify-content: flex-end;
		}
		.legend ul li {
			margin-left: 15px;
		}
		.legend ul li span {
			width: 40px;
		}
	}
</style>
