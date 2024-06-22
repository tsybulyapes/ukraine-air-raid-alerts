<script>
	import PieChart from '../components/PieChart.svelte';
	import BarChart from '../components/BarChart.svelte';
	import Graph_1 from '../components/Graph_1.svelte';
	import MapUa from '../components/MapUA.svelte';
	import ScatterPlot from '../components/ScatterPlot.svelte';
	import Text from '../components/Text.svelte';
	import Footer from '../components/Footer.svelte';
	import Header from '../components/Header/Header.svelte';

	let introText = [
		'Війна в Україні, відколи росія анексувала Крим та вдерлась на Схід країни, триває вже понад 10 років. При цьому від початку повномасштабного вторгнення минуло вже понад 2 роки, і весь цей час Україна знаходиться під щільними обстрілами рф. Ударів зазнають не тільки прифронтові регіони, де тривають активні бойові дії, але й мирні міста по всій території країни.',

		'На жаль, світом активно шириться наратив про те, що Захід втомився від України та війни, однак, втому українців та бійців ЗСУ, що боронять свою країну, знищуючи російську нечисть, важко уявити, або хоча б передати словами. А тому, підтримка України сьогодні – це не тільки допомога тим, хто стоїть на варті світової безпеки, але й особистий внесок у відновлення справедливості та перемогу добра на Землі.'
	];

	let headLine = 'повітряних тривог було оголошено<br />в Україні з 24 лютого 2022 року';

	let footerText = [
		'Проєкт створено в рамках навчання у Львівській національній академії мистецтв як курсове завдання з дисципліни Інформаційний дизайн.',
		'Виконала: <strong>Катерина Березіна.</strong><br />Викладач: <strong>Роман Свердан.</strong><br />Допомога та підтримка: <strong>Олег Нісс.</strong>',
		"Джерело: <a href='https://github.com/Vadimkin/ukrainian-air-raid-sirens-dataset?tab=readme-ov-file' target='_blank'>Дані повітряних тривог в Україні під час повномасштабного вторгнення рф</a>",
		"<span class='slava'><strong>Слава Україні!</strong></span>"
	];
</script>

<main>
	<Header headline={headLine} />

	<div class="center">
		<Text text={introText} />
		<MapUa />
		<Graph_1 />
		<ScatterPlot />
		<BarChart />
		<PieChart />
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
