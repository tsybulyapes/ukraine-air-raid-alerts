<script>
  import CounterV2 from "./CounterV2.svelte";
  import ScrollDown from "./ScrollDown.svelte";
  import Navigation from "./Navigation.svelte";
  export let headline = ''
</script>

<div class="header">
  <div class="overlay">
    <Navigation />
    <div class="container">
    <CounterV2 text={headline}/>
    <div class="icon">
      <ScrollDown />
    </div>
  </div>
</div>
</div>

<style>
	.header {
		width: 100%;
		height: 100vh;
		background-image: url(dmytro-tolokonov-g5rzXf74v5U-unsplash.jpg);
	}
	.overlay {
		width: 100%;
		height: 100vh;
		background-color: rgb(0 0 0 / 75%);
		position: absolute;
		top: 0;
		left: 0;
		z-index: 1;
	}
	.container {
		max-width: 900px;
		width: 100%;
		height: 100vh;
		display: flex;
		margin: 0 auto;
		align-items: center;
		z-index: 2;
	}
	.icon {
		position: absolute;
		bottom: 100px;
		left: 50%;
		margin-left: -10px;
	}
</style>
