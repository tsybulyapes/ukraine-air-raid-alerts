<script>
	export let placeholder = '';
	export let dropdownItems = [];
	export let selectedItem = '';
  import { regions } from './store'
  
  export let lang = 'ua'

	let selectOpen = false;
	const selectHandler = () => {
		selectOpen = !selectOpen;
	};

	const handleClick = (e) => (selectedItem = e);
</script>

<!-- svelte-ignore a11y-no-static-element-interactions -->
<div class="select" class:open={selectOpen} on:click={selectHandler} on:keydown={selectHandler}>
	<span>{lang != 'ua' ? regions[placeholder] : placeholder}</span>
	<ul class="dropdown">
		{#each dropdownItems as d}
			<!-- svelte-ignore a11y-no-noninteractive-element-interactions -->
			<li on:click={() => handleClick(d)} on:keydown={() => handleClick(d)}>
				{lang != 'ua' ? regions[d] : d}
			</li>
		{/each}
	</ul>
</div>

<style>
	.select {
		width: 40%;
		padding: 10px 15px;
		min-width: 250px;
		font-size: 16px;
		border: 1px solid #fff;
		border-radius: 5px;
		position: relative;
		cursor: pointer;
		font-family: 'Unbounded', sans-serif;
		font-weight: 300;
		font-size: 0.85rem;
	}

	.select::after {
		position: absolute;
		right: 10px;
		top: 16px;
		color: #fff;
		margin-top: 1px;
		border-color: #fff transparent transparent;
		border-style: solid;
		border-width: 5px 5px 0;
		content: '';
		transition: 200ms ease-out;
	}

	.select.open::after {
		transform: rotate(-180deg);
	}

	.dropdown {
		border-radius: 5px;
		position: absolute;
		top: calc(100% + 10px);
		left: -1px;
		display: none;
		min-width: 249px;
		max-height: 250px;
		width: 100%;
		overflow: auto;
		background: #000;
		border-radius: 4px;
		margin: 0;
		padding: 0 0 10px 0;
		border: 1px solid #fff;
		list-style: none;
		z-index: 9999;
		box-shadow: -2px 9px 28px -7px rgba(0, 0, 0, 0.45);
		-webkit-box-shadow: -2px 9px 28px -7px rgba(0, 0, 0, 0.45);
		-moz-box-shadow: -2px 9px 28px -7px rgba(0, 0, 0, 0.45);
	}

	.select.open .dropdown {
		display: block;
	}

	.dropdown li {
		font-family: var(--font-sans-serif);
		font-size: 0.85rem;
		padding: 5px 15px;
		cursor: pointer;
	}

	.dropdown li:hover {
		background-color: rgb(227, 1, 1, 0.5);
	}

	.dropdown li:active {
		background-color: rgb(227, 1, 1, 0.5) !important;
	}
</style>
