<script lang="ts">
	import { onMount } from 'svelte';
	import * as d3 from 'd3';

	export let data;
	export let year;
	export let pillarColor;
	export let colors;
	let treemapContainer;
	export let treemapContainerProps;
	onMount(() => {
		const chartContainerElementSize = treemapContainer.getBoundingClientRect();
		const width = treemapContainerProps.width
			? treemapContainerProps.width
			: chartContainerElementSize.width;
		const height = treemapContainerProps.height
			? treemapContainerProps.height
			: chartContainerElementSize.height;

		treemapContainerProps = {
			width: treemapContainerProps.width
				? treemapContainerProps.width
				: chartContainerElementSize.width,
			height: treemapContainerProps.height
				? treemapContainerProps.height
				: chartContainerElementSize.height
		};
		console.log(width, height);
		const chart = () => {
			const subcategories = [...new Set(data.map((item) => item.pillar_subsubcategory_name))];
			const newData = subcategories.map((subCategory) => {
				const filteredData = data.filter((item) => item.pillar_subsubcategory_name === subCategory);
				return { name: subCategory, children: filteredData };
			});
			data = {
				name: data[0].pillar_subcategory_name,
				children: newData
			};
			const format = d3.format(',d');

			const root = treemap(data, width, height);

			const svg = d3
				.select(treemapContainer)
				.append('svg')
				// .attr('viewBox', [0, 0, width, height])
				.attr('width', width)
				.attr('height', height)
				.style('font', '10px sans-serif')
				.style('background-color', 'black');

			const leaf = svg
				.selectAll('g')
				.data(root.leaves())
				.join('g')
				.attr('transform', (d) => `translate(${d.x0},${d.y0})`);
			const svgContainer = d3.select(treemapContainer);
			const textPadding = 16;
			const texty = svgContainer
				.selectAll('section > div')
				.data(root.leaves())
				.join('div')
				.attr(
					'style',
					(d) => `
						transform: translate(${d.x0 + 8}px, ${d.y0 + 8}px);
						position: absolute;
						top: 0px;
						left: 0px;
						width: ${d.x1 - d.x0 - 8}px;
						height: ${d.y1 - d.y0 - 8}px;
						color: var(--theme-color);
						overflow: hidden;
						hyphens: auto;
					`
				)
				.attr('class', 'treemap-rect-text')
				.html((d) => {
					const formattedValue = format(d.value);
					let unit = d.data.unit;
					if (unit === 'CO2eq') unit = 'CO<sub>2</sub>';

					return `
						<p>${formattedValue}<span class="unit">${unit}</span></p>
						<p><span class="unit">${d.data.name}</span></p>
					`;
				});

			leaf
				.append('rect')
				.attr('fill', (d) => {
					while (d.depth > 1) d = d.parent;
					const currentColor = colors.find((color) => color.name === d.data.name);
					return currentColor.value;
				})
				.attr('fill-opacity', 1)
				.attr('width', (d) => d.x1 - d.x0)
				.attr('height', (d) => d.y1 - d.y0);

			leaf.append('title').text(
				(d) =>
					`${d
						.ancestors()
						.reverse()
						.map((d) => d.data.name)
						.join('/')}\n${format(d.value)}`
			);
			// leaf
			// 	.append('clipPath')
			// 	.attr('id', (d) => (d.clipUid = uid(3)))
			// 	.append('use')
			// 	.attr('xlink:href', (d) => d.clipUid);
			// leaf
			// 	.append('text')
			// 	.attr('clip-path', (d) => d.clipUid)
			// 	.selectAll('tspan')
			// 	.data((d) => {
			// 		const formattedValue = format(d.value);
			// 		const unit = d.data.unit;
			// 		return [
			// 			{ text: formattedValue, fontSize: '1.15rem', yOffset: 1.1 },
			// 			{ text: unit, fontSize: '0.75rem', xOffset: 1.5 }
			// 		];
			// 	})
			// 	.join('tspan')
			// 	.attr('x', 32)
			// 	.attr('dy', 0)
			// 	.style('font-size', (d) => d.fontSize)
			// 	.style('font-family', 'FamiljenGrotesk')
			// 	.text((d) => d.text);

			// leaf
			// 	.selectAll('tspan')
			// 	.data((d) => {
			// 		const formattedValue = format(d.value);
			// 		const unit = d.data.unit;

			// 		return [
			// 			{ text: formattedValue, yOffset: 1.1, xOffset: 16 },
			// 			{ text: unit, xOffset: 1.5 }
			// 		];
			// 	})
			// 	.join('tspan')
			// 	.attr('x', (d, i, nodes) => {
			// 		if (i === 1) {
			// 			const previousWidth = nodes[0].getComputedTextLength();
			// 			return previousWidth + Number(nodes[0].getAttribute('x') || 0) + Number(d.xOffset || 0);
			// 		}
			// 		return d.xOffset || 0;
			// 	})
			// 	.attr('dy', (d, i) => (i === 0 ? '0' : `${d.yOffset || 0}em`))
			// 	.attr('y', 32)
			// 	.text((d) => d.text);

			// const text = leaf.append('text').attr('class', 'hi');

			// text
			// 	.selectAll('text.hi')
			// 	.data((d, i, nodes) => {
			// 		const nameParts = d.data.name.split(/(?=[A-Z][a-z])|\s+/g);
			// 		// const sentenceWidth = tspan.reduce((a, b) => a + b.getComputedTextLength());
			// 		return nameParts;
			// 	})
			// 	.join('tspan')
			// 	.attr('x', 16)
			// 	// .attr('dy', (d, i) => (0}em`))
			// 	.attr('y', (d, i, nodes) => `${3 + i}rem`)
			// 	.text((d) => d)
			// 	.style('font-size', '0.6rem');

			return svg.node();
		};

		function treemap(data, width, height) {
			return d3.treemap().size([width, height]).padding(1).round(true)(
				d3
					.hierarchy(data)
					.sum((d) => d.value)
					.sort((a, b) => b.value - a.value)
			);
		}
		chart();
	});
</script>

<section bind:this={treemapContainer} />

<style>
	section {
		flex: 1 1 100%;
		width: 100%;
		position: relative;
	}
	section > :global(svg text.value) {
		font-size: 1rem;
	}
	section > :global(*) {
		font-family: 'FamiljenGrotesk';
		letter-spacing: -0.05em;
	}
	section > :global(.treemap-rect-text > p .unit) {
		font-size: 0.75rem;
	}
	section > :global(.treemap-rect-text > p) {
		font-size: 1rem;
		color: var(--theme-color);
		margin: 0;
	}
</style>
