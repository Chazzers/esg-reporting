<script>
	import * as d3 from 'd3';
	import { onMount } from 'svelte';
	export let data;
	export let pillarColor;

	const format = d3.format(',d');

	// Data for two circles with varying radii
	onMount(() => {
		const circles = document.querySelectorAll('.circle');
		const container = document.querySelector('.container');
		const containerRect = container.getBoundingClientRect();
		const { width, height } = containerRect;
		const canvas = document.getElementById('line-canvas');
		const flexContainer = document.querySelector('.flex-container');

		function drawLines(canvas, position, circleAspectRatio) {
			const { width, height } = flexContainer.getBoundingClientRect();
			if (!canvas.getContext) {
				return;
			}
			canvas.setAttribute('width', width * 2);
			canvas.setAttribute('height', height * 2);
			const ctx = canvas.getContext('2d');
			const canvasRatio = 2;
			window.devicePixelRatio = canvasRatio;
			ctx.translate(0.5, 0.5);
			ctx.scale(canvasRatio, canvasRatio);

			// set line stroke and line width
			ctx.strokeStyle = 'white';
			ctx.lineWidth = 2;

			// draw line0
			console.log(position);
			const line0 = position.line0;
			ctx.beginPath();
			ctx.setLineDash([5]);
			ctx.moveTo(line0.x0, line0.y0);
			ctx.lineTo(line0.x1, line0.y1);
			ctx.stroke();

			const line1 = position.line1;
			ctx.beginPath();
			ctx.setLineDash([5]);
			ctx.moveTo(line0.x0, line1.y0);
			ctx.lineTo(line0.x1, line1.y1);
			ctx.stroke();
		}

		data = data.filter((item) => item.unit !== '%').map((item) => ({ ...item, r: item.value }));

		const max = data.reduce((a, b) => a + b.r, 0);

		const size = d3
			.scaleLinear()
			.domain([0, max])
			.range([0, Math.min(width, height)]);
		const position = {};
		circles.forEach((circle, i) => {
			circle.style.width = size(data[i].r) + 'px';
			circle.style.height = size(data[i].r) + 'px';
		});

		const circle1 = circles[0];
		const circle2 = circles[1];
		const circle1Rect = circle1.getBoundingClientRect();
		const circle2Rect = circle2.getBoundingClientRect();

		position['line0'] = {};
		position['line0'].x0 = circle1Rect.width / 2;
		position['line0'].y0 = 0;
		position['line0'].x1 = circle2Rect.x - circle1Rect.x + circle2Rect.width / 2;
		position['line0'].y1 = 0;

		position['line1'] = {};
		position['line1'].x0 = circle1Rect.width / 2;
		position['line1'].y0 = circle1Rect.bottom - circle1Rect.y;
		position['line1'].x1 = circle2Rect.right - circle2Rect.width / 2 - circle2Rect.x / 2;
		console.log(circle2Rect.right - circle2Rect.width / 2);
		position['line1'].y1 = circle2Rect.bottom - circle2Rect.y;

		drawLines(canvas, position, circle2Rect.width / circle1Rect.width);
	});
</script>

<div class="container">
	<div class="flex-container">
		<div style="--pillar-color: {pillarColor}" class="circle">
			<div>
				<p>{format(data[0].value)}<span class="unit">{data[0].unit}</span></p>
				<p><span class="unit">{data[0].name}</span></p>
			</div>
		</div>
		<div style="--pillar-color: {pillarColor}" class="circle">
			<div>
				<p>{format(data[1].value)}<span class="unit">{data[1].unit}</span></p>
				<p style="line-height: 0.8"><span class="unit">{data[1].name}</span></p>
			</div>
		</div>
		<canvas id="line-canvas" />
	</div>
</div>

<style>
	.circle {
		border-radius: 50%;
		background-color: var(--pillar-color);
		position: relative;
		padding: 1em;
		z-index: 1;
		display: flex;
		justify-content: center;
		align-items: center;
	}
	.circle > div > p {
		font-size: 1rem;
		color: var(--theme-color);
		margin: 0;
		font-family: 'FamiljenGrotesk';
		letter-spacing: -0.05em;
	}
	.circle div > p span.unit {
		font-size: 0.9rem;
	}
	.container {
		width: 100%;
		height: 100%;
		display: flex;
	}

	.flex-container {
		display: flex;
		position: relative;
		height: 100%;
		width: 100%;
	}

	canvas {
		position: absolute;
		z-index: 0;
		left: 0;
		top: 0;
		width: 100%;
		height: 100%;
	}
</style>
