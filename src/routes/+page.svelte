<script lang="ts">
	export let data;
	import { page } from '$app/stores';
	import EsgScoreSection from '../components/esgScoreSection/EsgScoreSection.svelte';
	import LandSection from '../components/landSection/LandSection.svelte';
	import { writable } from 'svelte/store';
	import ExploreMoreSection from '../components/ExploreMoreSection/ExploreMoreSection.svelte';
	import CompanyRankingSection from '../components/sections/CompanyRankingSection.svelte';
	const count = writable(0);

	// const pillars = data.data.map((item) => item.group);
	const companyA = data.data.find((company) => company.name === 'CompanyA');
	const currentYear = '2026';
	const nonPillar = ['ESG', 'categoryScores'];
	const pillars = Object.keys(companyA.score).filter((item) => !nonPillar.includes(item));
	const summedPillarScores =
		companyA.score.Environment[currentYear] +
		companyA.score.Social[currentYear] +
		companyA.score.Governance[currentYear];

	const gradientValues = pillars.map(
		(pillar) => (companyA.score[pillar][currentYear] / summedPillarScores) * 100
	);

	const radialGradientString = `conic-gradient(#BCDA7E 5% ${gradientValues[0] * 0.9}%, #6257E2  ${
		gradientValues[0] * 1.1
	}% ${(gradientValues[0] + gradientValues[1]) * 0.9}%, #2AC28B ${
		(gradientValues[0] + gradientValues[1]) * 1.1
	}% 95%, #BCDA7E 100%)`;

	const contentPast = 'This is companyAs 2025 ESG industry impact score.';
	const contentPresent = 'This is companyAs 2026 ESG industry impact score.';
	const contentFuture = 'This is companyAs 2030 targeted ESG industry impact score.';
	const companyAContent = {
		tag: 'Our purpose is to move the world forward through the power of sport'
	};
</script>

<LandSection
	companyData={companyA}
	{data}
	{radialGradientString}
	year={'2026'}
	content={companyAContent}
/>

<EsgScoreSection companyData={companyA} content={contentPast} {count} year={'2025'} />

<EsgScoreSection companyData={companyA} content={contentPresent} {count} year={'2026'} />

<EsgScoreSection companyData={companyA} content={contentFuture} {count} year={'2030'} />

<CompanyRankingSection {data} year={'2026'} {count} />
<ExploreMoreSection {page} />

<style>
	:global(#title) {
		font-weight: 700;
		font-size: x-large;
	}

	:global(text.tiny) {
		font-size: 10pt;
	}
	:global(text.light) {
		fill: lightgrey;
	}

	:global(.world) {
		stroke: lightgrey;
		stroke-width: 4px;
	}

	:global(.cell) {
		stroke: white;
		stroke-width: 1px;
	}

	:global(.label) {
		text-anchor: middle;
		fill: white;
	}

	:global(.label > .name) {
		dominant-baseline: text-after-edge;
	}

	:global(.label > .value) {
		dominant-baseline: text-before-edge;
	}

	:global(.hoverer) {
		fill: transparent;
		stroke: white;
		stroke-width: 0px;
	}

	:global(.hoverer:hover) {
		stroke-width: 3px;
	}

	:global(.legend-color) {
		stroke-width: 1px;
		stroke: darkgrey;
	}
</style>
