<script lang="ts">
	import { page } from '$app/stores';
	import Answer from './Answer.svelte';
	import EmojiQuestion from './EmojiQuestion.svelte';
	import PackSelect from './PackSelect.svelte';
	import Question from './Question.svelte';
	import Table from './Table.svelte';

	function parseNumber(str: string | null) {
		return str == null ? null : parseInt(str);
	}

	function parseNumberPair(str: string | null) {
		return str == null ? null : str.split(':').map(parseNumber);
	}

	function parseNumberPairList(str: string | null) {
		return str == null ? [] : str.split(',').map(parseNumberPair);
	}

	$: packIdx = parseNumber($page.url.searchParams.get('packIdx'));
	$: questionsDone = parseNumberPairList($page.url.searchParams.get('questionsDone')) as number[][];
	$: questionInProgress = parseNumberPair($page.url.searchParams.get('questionInProgress')) as number[];
	$: isAnswering = $page.url.searchParams.get('isAnswering') === 'true';
	$: isEmoji = $page.url.searchParams.get('isEmoji') === 'true';

	$: everything = JSON.stringify({
		packIdx,
		questionsDone,
		questionInProgress,
		isAnswering,
	});
</script>

<main>
	<div class="content-container">
		{#if packIdx == null}
			<PackSelect />
		{:else if isAnswering}
			<Answer {packIdx} {questionInProgress} {questionsDone} />
		{:else if questionInProgress != null}
			{#if isEmoji}
				<EmojiQuestion {packIdx} {questionInProgress} />
			{:else}
				<Question {packIdx} {questionInProgress} />
			{/if}
		{:else}
			<Table {questionsDone} {packIdx} />
		{/if}
	</div>
</main>

<style>
	:global(*) {
		box-sizing: border-box;
	}

	:global(body) {
		margin: 0;
		font-family: 'Trebuchet MS', 'Lucida Sans Unicode', 'Lucida Grande', 'Lucida Sans', Arial, sans-serif;
		background-image: linear-gradient(rgba(255, 255, 255, 0.1), rgba(255, 255, 255, 0.1)), url('background-blurred.jpg');
		background-size: cover;
		color: whitesmoke;
	}

	:global(a) {
		color: inherit;
		text-decoration: none;
	}

	:global(.hover-zoom:not(.disabled)) {
		transition: transform 100ms linear;
		cursor: pointer;
	}

	:global(.hover-zoom:not(.disabled):hover) {
		transform: scale(1.2);
	}

	:global(.box) {
		display: block;
		background-color: rgba(0, 0, 0, 0.8);
		border-radius: 1vh;
	}

	main {
		height: 100vh;
	}

	.content-container {
		height: 100%;
		display: flex;
		justify-content: center;
		align-items: center;
		flex-direction: column;
	}
</style>
