<script>
	export let pillarColor;
	export let showString;
</script>

<svg width="206" height="140" viewBox="0 0 206 140" fill="none" xmlns="http://www.w3.org/2000/svg">
	<g clip-path="url(#clip0_273_2551)">
		<rect
			x="20"
			y="95"
			width="42"
			height="45"
			fill={showString === 'table' ? pillarColor : '#041235'}
		/>
		<rect
			x="82"
			y="5"
			width="42"
			height="135"
			fill={showString === 'table' ? pillarColor : '#041235'}
		/>
		<rect
			x="144"
			y="60"
			width="42"
			height="80"
			fill={showString === 'table' ? pillarColor : '#041235'}
		/>
		<path
			d="M0 140H206"
			stroke={showString === 'table' ? pillarColor : '#041235'}
			stroke-width="8"
		/>
	</g>
	<defs>
		<clipPath id="clip0_273_2551">
			<rect width="206" height="140" fill="white" />
		</clipPath>
	</defs>
</svg>

<style>
	svg {
		width: 100%;
		height: 100%;
	}
</style>
