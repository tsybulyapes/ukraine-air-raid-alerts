<script>
  export let content = []
</script>

<main>
	<div class="main">
    {#each content as c}
      <p>{@html c}</p>
    {/each}
		
	</div>
</main>

<style>
	main {
		background-color: black;
		width: 100%;
		max-width: 700px;
		margin-top: 10rem;
		margin-bottom: 7rem;
		margin-right: auto;
		margin-left: auto;
	}

	.main {
		height: 500px;
		display: flex;
		flex-direction: column;
		justify-content: center;
	}

	p {
		font-weight: 300;
		font-size: 1rem;
    line-height: 1.8;
		font-family: 'Unbounded', sans-serif;
		color: white;
		width: 100%;
  }
	
</style>
