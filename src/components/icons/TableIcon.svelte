<script>
	export let pillarColor;
	export let showString;
</script>

<svg width="208" height="142" viewBox="0 0 208 142" fill="none" xmlns="http://www.w3.org/2000/svg">
	<path
		d="M1 11C1 5.47715 5.47715 1 11 1H104V71H1V11Z"
		stroke={showString === 'chart' ? pillarColor : '#041235'}
		stroke-width="2"
	/>
	<path
		d="M1 71H104V141H11C5.47715 141 1 136.523 1 131V71Z"
		stroke={showString === 'chart' ? pillarColor : '#041235'}
		stroke-width="2"
	/>
	<path
		d="M104 1H197C202.523 1 207 5.47715 207 11V71H104V1Z"
		stroke={showString === 'chart' ? pillarColor : '#041235'}
		stroke-width="2"
	/>
	<path
		d="M104 71H207V131C207 136.523 202.523 141 197 141H104V71Z"
		stroke={showString === 'chart' ? pillarColor : '#041235'}
		stroke-width="2"
	/>
</svg>

<style>
	svg {
		width: 100%;
		height: 100%;
	}
	svg path {
		stroke-width: 4px;
	}
</style>
