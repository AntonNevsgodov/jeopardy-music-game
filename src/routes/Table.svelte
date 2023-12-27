<script lang="ts">
	import config from './config.json';
	import { page } from '$app/stores';
	import { onMount } from 'svelte';
	import { goto } from '$app/navigation';

	export let packIdx: number;
	export let questionsDone: number[][];

	$: console.log(questionsDone);
	$: pack = config[packIdx];

	function getQuestionOptions(categoryIdx: number, questionIdx: number) {
		const disabled = questionsDone.some(([c, q]) => c === categoryIdx && q === questionIdx);
		const newQuery = new URLSearchParams($page.url.searchParams);
		newQuery.set('questionInProgress', `${categoryIdx}:${questionIdx}`);
		newQuery.set('isEmoji', `${'emojisPath' in pack.categories[categoryIdx].songs[questionIdx]}`);
		return { href: disabled ? 'javascript:void(0)' : `?${newQuery.toString()}`, disabled };
	}

	onMount(() => {
		if (questionsDone.length === 25) goto('/');
	});
</script>

<section>
	{#each pack.categories as category, categoryIdx}
		<div class="row">
			<div class="category-name box">
				{category.categoryName}
			</div>
			{#each category.songs as song, questionIdx}
				{@const { href, disabled } = getQuestionOptions(categoryIdx, questionIdx)}
				<a {href} class="song-points box hover-zoom" class:disabled>
					{song.points}
				</a>
			{/each}
		</div>
	{/each}
</section>

<style>
	section {
		height: 70%;
		width: 80%;
		display: flex;
		flex-direction: column;
		justify-content: space-between;
	}

	.row {
		display: flex;
		gap: 3%;
	}

	.category-name,
	.song-points {
		padding: 3vh;
		font-size: 3vh;
	}

	.category-name {
		margin-right: auto;
		width: 40%;
		text-align: center;
	}

	.disabled {
		color: transparent;
	}
</style>
