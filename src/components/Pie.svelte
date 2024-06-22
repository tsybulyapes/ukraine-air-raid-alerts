<script>
	import { arc, transition } from 'd3';
	import * as d3 from 'd3';
	import viewport from './tools/useViewportAction';

	export let width;
	export let height;
	export let arcs;

	let animate = false;

	const options = {
		threshold: [0.5] // half height of the viewport
	};

	const innerRadius = width / 5;

	const arcPath = arc()
		.innerRadius(innerRadius)
		.outerRadius(Math.min(width, height) / 2 - 15);

	let svgElement;

	$: if (animate) {
		animateArcs(svgElement);
	}

	function animateArcs(svg) {
		const path = d3.select(svg).selectAll('path')
			.data(arcs.pieChartData);

		path.transition()
			.duration(1000)
			.attrTween('d', function(d) {
				const interpolate = d3.interpolate({ startAngle: 0, endAngle: 0 }, d);
				return function(t) {
					return arcPath(interpolate(t));
				};
			});
	}
</script>

<div class="pie-chart" 
  use:viewport={options}
  on:enterViewport={() => animate = true}>

	<h3 style:opacity={animate ? 1 : 0}>{@html arcs.name.replace(' ', '<br>')}</h3>
	
	<div class="pie-chart__content" bind:clientWidth={width} bind:clientHeight={height}>
		<svg bind:this={svgElement} {width} {height} viewBox="{-width / 2}, {-height / 2}, {width}, {height}">
			{#each arcs.pieChartData as segment}
				<g style:opacity={animate ? 1 : 0}>
          <path d={arcPath(segment)} fill={segment.data.fill} />
          <text style:opacity={animate ? 1 : 0} class="values" x={-width / 8} y={height / 32} font-family="Unbounded" font-size="12" fill="white">
            {arcs.percentage}%
          </text>
        </g>
			{/each}
		</svg>
	</div>
</div>

<style>
	.pie-chart {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
	}

	.pie-chart__content {
		width: 100%;
		height: 100%;
		max-width: 200px;
		max-height: 200px;
	}

	h3 {
		font-size: 0.85rem;
		font-weight: 400;
		margin-bottom: 0px;
		margin-top: 20px;
		text-align: center;
    transition: all 1s;
	}
  .values {
    transition: all 1s;
  }
</style>
