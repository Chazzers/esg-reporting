<script lang="ts">
	import ChartIcon from '../../icons/ChartIcon.svelte';
	import TableIcon from '../../icons/TableIcon.svelte';
	import Legend from '../charts/Legend.svelte';
	import DataTableGhg from '../content/DataTableGHG.svelte';
	import { fade } from 'svelte/transition';
	export let chartString: string;
	export let data;
	export let year;
	export let colors;
	export let category;
	export let pillarColor;
	export let tableContent;
	const charts = {
		Treemap: () => import('../charts/Treemap.svelte'),
		CircleWaste: () => import('../charts/CircleWaste.svelte'),
		WaterBars: () => import('../charts/WaterBars.svelte'),
		TopManagement: () => import('../charts/TopManagement.svelte'),
		FulltimeParttime: () => import('../charts/FulltimeParttime.svelte'),
		MaleFemaleTotal: () => import('../charts/MaleFemaleTotal.svelte'),
		PaymentPractices: () => import('../charts/PaymentPractices.svelte'),
		CorruptionBribery: () => import('../charts/CorruptionBribery.svelte'),
		GHGEmissionsChart: () => import('../charts/GHGEmissionsChart.svelte'),
		HealthSafety: () => import('../charts/HealthSafety.svelte'),
		CircleChart: () => import('../charts/CircleChart.svelte')
	};
	let storeData = data;
	if (chartString === 'Treemap') {
		const dontInclude = [
			'Total GHG emissions',
			'Total GHG Scope 3',
			'GHG intensity based on net revenue',
			'Total energy consumption non-renewable sources',
			'Total energy consumption renewable sources',
			'Energy intensity based on net revenue',
			'Share of renewable energy from total',
			'Energy consumption total'
		];
		storeData = data.filter((item) => !dontInclude.includes(item.name));
	}
	$: showString = 'chart';
	const viewSwitch = (event) => {
		const { value } = event.currentTarget;

		showString = value;
	};
	let chartContainerElement;
	let animContainer;
	$: treemapContainerProps = { width: null, height: null };
</script>

<section
	bind:this={chartContainerElement}
	style="--align: {chartString === 'Treemap' ? 'center' : 'flex-end'}"
>
	<div>
		{#await charts[chartString]() then module}
			{#if showString === 'chart'}
				<div
					bind:this={animContainer}
					in:fade={{ delay: 250, duration: 300 }}
					out:fade={{ delay: 0, duration: 0 }}
				>
					<svelte:component
						this={module.default}
						data={storeData}
						{year}
						{colors}
						bind:treemapContainerProps
						{pillarColor}
					/>

					{#if chartString === 'Treemap'}
						<Legend {colors} {category} />
					{/if}
				</div>
			{:else}
				<div in:fade={{ delay: 250, duration: 300 }} out:fade={{ delay: 0, duration: 0 }}>
					<DataTableGhg {tableContent} {pillarColor} {chartContainerElement} />
				</div>
			{/if}
		{/await}
	</div>

	<div class="buttons-container" style="--pillar-color: {pillarColor}">
		<button
			on:click={viewSwitch}
			class={showString === 'chart' ? 'active' : ''}
			value="chart"
			type="button"
		>
			<div class="icon-container">
				<ChartIcon {pillarColor} {showString} />
			</div>
			<p>Chart</p>
		</button>
		<button
			on:click={viewSwitch}
			class={showString === 'table' ? 'active' : ''}
			value="table"
			type="button"
		>
			<div class="icon-container">
				<TableIcon {pillarColor} {showString} />
			</div>
			<p>Table</p>
		</button>
	</div>
</section>

<style>
	section {
		flex: 1 1 66%;
		display: flex;
		justify-content: space-between;
	}
	section > div:first-of-type {
		display: flex;
		width: 100%;
		height: 100%;
		flex-direction: column;
	}

	section > .buttons-container {
		margin-left: 1rem;
		display: flex;
		flex-direction: column;
	}
	section > div > div {
		display: flex;
		width: 100%;
		height: 100%;
		flex-direction: column;
	}
	section > .buttons-container > button {
		height: 5rem;
		width: 5rem;
		display: flex;
		flex-direction: column;
		border-radius: 10px;
		border: solid 1px var(--pillar-color);
		background-color: transparent;
	}
	section > .buttons-container > button:last-of-type {
		margin-top: 1rem;
	}

	section > .buttons-container > button > p {
		margin: 0;
		font-size: 1rem;
		text-align: center;
		width: 100%;
		color: var(--pillar-color);
	}
	section > .buttons-container > button.active {
		background-color: var(--pillar-color);
	}
	section > .buttons-container > button.active p {
		color: var(--theme-color);
	}
</style>
