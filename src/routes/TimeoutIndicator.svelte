<script>
	export let duration = 10;
	export let strokeWidth = 20;
	export let paused = false;

	$: radius = Math.floor((100 - strokeWidth) / 2);
	$: dashArray = Math.ceil(2 * Math.PI * radius);

	$: styles = [`animation-duration: ${duration}ms`, `--max-dash-array: ${dashArray}`];
</script>

<div class="component-container">
	<svg viewBox="0 0 100 100">
		<circle class="bg-circle" cx="50" cy="50" r={radius} stroke-width={strokeWidth} fill-opacity="0" />
		<circle
			class="fg-circle"
			style={styles.join('; ')}
			cx="50"
			cy="50"
			r={radius}
			stroke-width={strokeWidth}
			fill-opacity="0"
			style:animation-play-state={paused ? 'paused' : 'running'}
		/>
	</svg>
	<div class="slot-container">
		<slot />
	</div>
</div>

<style>
	.component-container {
		position: relative;
		width: 100%;
		height: 100%;
	}

	.slot-container {
		position: absolute;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
	}

	svg {
		transform: scaleY(-1);
		height: 100%;
		width: 100%;
	}

	.fg-circle {
		--undraw-duration: 10s;
		stroke-dasharray: var(--max-dash-array);
		stroke-dashoffset: var(--max-dash-array);
		animation: undrawing var(--undraw-duration) linear;
		stroke: rgba(255, 255, 255);
	}

	.bg-circle {
		stroke: rgba(255, 255, 255, 0.1);
	}

	@keyframes undrawing {
		from {
			stroke-dashoffset: 0;
		}
		to {
			stroke-dashoffset: var(--max-dash-array);
		}
	}
</style>
