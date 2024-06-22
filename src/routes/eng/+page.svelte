<script>
	import PieChart from '../../components/PieChart.svelte';
	import BarChart from '../../components/BarChart.svelte';
	import Graph_1 from '../../components/Graph_1.svelte';
	import MapUa from '../../components/MapUA.svelte';
	import ScatterPlot from '../../components/ScatterPlot.svelte';
	import Text from '../../components/Text.svelte';
	import Footer from '../../components/Footer.svelte';
	import CounterV2 from '../../components/Header/CounterV2.svelte';
	import Header from '../../components/Header/Header.svelte';

	let introText = [
		'The war in Ukraine, since Russia annexed Crimea and invaded the East of the country, has been ongoing for over 10 years. Since the beginning of the full-scale invasion, over 2 years have passed, and throughout this time Ukraine has been under heavy shelling from Russia. Strikes are not only on the front-line regions, where active combat operations are taking place, but also on peaceful cities throughout the country.',
		'Unfortunately, there is an active narrative spreading globally that the West is tired of Ukraine and the war. However, the exhaustion of Ukrainians and the Ukrainian Armed Forces fighters who defend their country, destroying the Russian evil, is difficult to imagine or even convey in words. Therefore, supporting Ukraine today is not only assistance to those who stand on the front lines of world security, but also a personal contribution to restoring justice and achieving victory of good on Earth.'
	];

	let headLine = 'air raid alerts were announced<br />in Ukraine since February 24, 2022';

	let footerText = [
		'The project was created as part of the coursework for the discipline of Information Design at the Lviv National Academy of Arts.',
		'Created by: <strong>Kateryna Berezina.</strong><br />Teacher: <strong>Roman Sverdan.</strong><br />Help and support: <strong>Oleh Niss.</strong>',
		"Data source: <a href='https://github.com/Vadimkin/ukrainian-air-raid-sirens-dataset?tab=readme-ov-file' target='_blank'>Data on air raid alarms in Ukraine during the full-scale invasion by the russian federation</a>",
		"<span class='slava'><strong>Glory to Ukraine!</strong></span>"
	];
</script>

<main>
	<Header headline={headLine} />

	<div class="center">
		<Text text={introText} />
		<MapUa lang="eng" title="Number of air raid alerts by regions" legendTitle="Alerts total" />
		<Graph_1
			lang="eng"
			title="Graph of the number of air raid alarms"
			subTitle="For each year of the full-scale invasion"
		/>
		<ScatterPlot
			lang="eng"
			title="Graph of the duration of air raid alarms"
			subTitle="For each day of the full-scale invasion according to the time of a day"
		/>
		<BarChart lang="eng" title="10 longest air raid alarms (excluding Luhansk region)" />
		<PieChart
			lang="eng"
			title="The ratio of time with alarms to time without alarms"
			subTitle="10 regions with the highest number of alarms"
		/>
		<Footer content={footerText} />
	</div>
</main>

<style>
	:global(body) {
		margin: 0;
		padding: 0;
		box-sizing: border-box;
		background-color: black;
	}

	main {
		height: 100%;
		overflow-x: hidden;
	}

	.center {
		width: calc(100% - 50px);
		max-width: 1140px;
		margin: 0 auto;
		padding: 0 25px;
		position: relative;
	}
</style>
