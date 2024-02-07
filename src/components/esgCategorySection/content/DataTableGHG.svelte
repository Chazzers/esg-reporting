<script>
	import { onMount } from 'svelte';
	import { fade } from 'svelte/transition';
	export let tableContent;
	export let pillarColor;
	export let chartContainerElement;
	import roundToDecimal from '../../../helpers/roundToDecimal';
	import * as d3 from 'd3';
	const format = d3.format(',d');
	const roundedTableContent = tableContent.map((item) => ({
		...item,
		values: {
			...item.values,
			2026: {
				...item.values[2026],
				value:
					item.unit === 'decimal'
						? roundToDecimal(item.values[2026].value, 1)
						: roundToDecimal(item.values[2026].value, 10)
			}
		}
	}));
	const grids = [];
	const chunkSize = 3;
	for (let i = 0; i < roundedTableContent.length; i += chunkSize) {
		const chunk = roundedTableContent.slice(i, i + chunkSize);
		grids.push(chunk);
	}
	grids.forEach((grid) => {
		if (grid.length < 2) {
			grid.push('');
			grid.push('');
		} else if (grid.length < 3) {
			grid.push('');
		}
	});
	let tableContainer;
	$: containerHeight = 0;
	$: chartContainerElementHeight = 0;
	onMount(() => {
		containerHeight = tableContainer.getBoundingClientRect().height;
		chartContainerElementHeight = chartContainerElement.getBoundingClientRect().height;
	});
</script>

<section
	style="
	overflow-y: {containerHeight > chartContainerElementHeight ? 'scroll' : 'none'};
	"
	bind:this={tableContainer}
>
	{#each grids as grid}
		<div
			style="--grid-columns: {3};
			 --grid-rows: 2;
			 "
		>
			{#if roundedTableContent}
				{#each grid as tableItem}
					{#if tableItem.name}
						<div class="table-header" style="--color: {pillarColor}">
							{tableItem.name}
						</div>
					{:else}
						<div />
					{/if}
				{/each}
				{#each grid as tableItem}
					{#if tableItem.name}
						<div style="--color: {pillarColor}">
							{#if tableItem.unit === '%'}
								{format(tableItem.values[2026].value)}<span
									>{tableItem.unit !== 'decimal' ? tableItem.unit : ''}</span
								>
							{:else}
								{format(tableItem.values[2026].value)}
								<span>
									{tableItem.unit !== 'decimal' ? tableItem.unit : ''}
								</span>
							{/if}
						</div>
					{:else}
						<div />
					{/if}
				{/each}
			{/if}
		</div>
	{/each}
</section>

<style>
	section {
		color: var(--text-color);
		height: auto;
	}
	section > div {
		display: grid;
		grid-template-columns: repeat(var(--grid-columns), 1fr);
		grid-template-rows: 2;
		margin-bottom: 1rem;
	}

	section > div > div {
		border-bottom: solid 1px var(--color);
		border-right: solid 1px var(--color);
		padding: 0.5rem;
	}
	section > div > div:nth-of-type(3n + 1) {
		border-left: solid 1px var(--color);
	}
	/* section > div > div > div:last-of-type {
		border: 1px solid white;
		padding: 0.5rem;
	} */
	.table-header {
		border-bottom: 1px solid var(--color);
		border-top: 1px solid var(--color);
		color: var(--color);
		font-weight: 500;
		text-wrap: balance;
	}

	section > div > div > div > span {
		font-size: 0.65rem;
	}
</style>
