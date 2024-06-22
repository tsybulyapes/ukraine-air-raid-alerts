<script>
  export let text = []
</script>

<main>
  {#each text as t}
    <p>{t}</p>
  {/each}
</main>

<style>
	p {
		font-weight: 300;
		font-size: 1rem;
		font-family: 'Unbounded', sans-serif;
		color: white;
		line-height: 2;
    margin-top: 30px;
  }

	main {
		max-width: 700px;
		max-height: 960px;
		margin: 10rem auto;
	}
</style>
