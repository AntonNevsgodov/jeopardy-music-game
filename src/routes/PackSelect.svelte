<script lang="ts">
	import config from './config.json';
</script>

<section>
	{#each config as pack, idx}
		<a href="?packIdx={idx}" class="hover-zoom box">
			{pack.packName}
		</a>
	{/each}
</section>

<style>
	section {
		display: flex;
		flex-direction: column;
		height: 100%;
		gap: 10%;
		justify-content: center;
		align-items: center;
		width: 100%;
	}

	a {
		display: block;
		width: 100%;
		font-size: 5vh;
		padding: 5vh;
		width: 50%;
		display: flex;
		justify-content: center;
	}
</style>
