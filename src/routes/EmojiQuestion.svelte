<script lang="ts">
	import { goto } from '$app/navigation';
	import { page } from '$app/stores';
	import config from './config.json';

	export let packIdx: number;
	export let questionInProgress: number[];

	$: question = config[packIdx].categories[questionInProgress[0]].songs[questionInProgress[1]] as any;

	function goNext() {
		const newQuery = new URLSearchParams($page.url.searchParams);
		newQuery.delete('isEmoji');
		newQuery.set('isAnswering', 'true');
		goto(`?${newQuery}`);
	}
</script>

<button class="box" on:click={goNext}>
	{#each Array(question.emojisN).fill(null) as _, idx}
		<img src="{question.emojisPath}/{idx + 1}.png" alt="" />
	{/each}
</button>

<style>
	button {
		width: 80%;
		height: 70%;
		display: flex;
		flex-wrap: wrap;
		gap: 10%;
		justify-content: center;
		align-items: center;
		padding: 5%;
		cursor: pointer;
	}
	img {
		width: 15%;
	}
</style>
