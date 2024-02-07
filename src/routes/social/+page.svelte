<script>
	import EsgCategorySection from '../../components/esgCategorySection/ESGCategorySection.svelte';

	export let data;
	const companyA = data.data.find((company) => company.name === 'CompanyA');
	const pillarColor = '#8B84DB';

	const tableContentManagement = companyA.values.filter(
		(item) =>
			item.name === 'Total number of top management' ||
			item.name === 'Percentage of female top management' ||
			item.name === 'Percentage of male top management'
	);

	const tableContentFulltimeParttime = companyA.values.filter(
		(item) =>
			item.name === 'Percentage difference of male female full-time employees' ||
			item.name === 'Percentage difference of male female part-time employees'
	);
	const tableContentMaleFemaleTotal = companyA.values.filter(
		(item) =>
			item.name === 'Total number of employees' ||
			item.name === 'Percentage of male employees' ||
			item.name === 'Percentage of female employees'
	);
	const tableContentHealth = companyA.values.filter(
		(item) => item.pillar_subcategory === 'healthAndSafety'
	);
	console.log(tableContentHealth);
</script>

<EsgCategorySection
	data={companyA}
	category={'employees'}
	chartString={'MaleFemaleTotal'}
	description={`These values represent CompanyA's employee gender distribution.`}
	{pillarColor}
	tableContent={tableContentMaleFemaleTotal}
/>
<EsgCategorySection
	data={companyA}
	category={'employmentStatus'}
	chartString={'FulltimeParttime'}
	description={`These values represent CompanyA's Fulltime and Parttime employee gender distribution.`}
	{pillarColor}
	tableContent={tableContentFulltimeParttime}
/>

<EsgCategorySection
	data={companyA}
	category={'topManagement'}
	chartString={'TopManagement'}
	description={`These values represent CompanyA's Top management gender distribution.`}
	{pillarColor}
	tableContent={tableContentManagement}
/>

<EsgCategorySection
	data={companyA}
	category={'healthAndSafety'}
	chartString={'HealthSafety'}
	description={`These values represent CompanyA's Health and Safety incident rate and lost time rate.`}
	{pillarColor}
	tableContent={tableContentHealth}
/>
