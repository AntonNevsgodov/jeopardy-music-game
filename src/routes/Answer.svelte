<script lang="ts">
	import { page } from '$app/stores';
	import { goto } from '$app/navigation';
	import config from './config.json';
	import { onMount, onDestroy } from 'svelte';

	export let packIdx: number;
	export let questionInProgress: number[];
	export let questionsDone: number[][];

	$: question = config[packIdx].categories[questionInProgress[0]].songs[questionInProgress[1]];
	let audio: HTMLAudioElement | null = null;
	let stopTimeout: ReturnType<typeof setTimeout> | null = null;

	function goToTable() {
		const newQuery = new URLSearchParams($page.url.searchParams);
		newQuery.delete('isAnswering');
		newQuery.delete('questionInProgress');
		questionsDone.push(questionInProgress);
		newQuery.set('questionsDone', questionsDone.map((a) => a.join(':')).join(','));
		goto(`?${newQuery}`);
	}

	function pauseAudio() {
		audio?.pause();

		if (stopTimeout) {
			clearTimeout(stopTimeout);
			stopTimeout = null;
		}
	}

	onMount(async () => {
		audio = new Audio(question.filename);

		await new Promise((res) => {
			audio?.addEventListener('loadeddata', res);
		});

		audio.currentTime = question.answerTimings.start;
		audio.play();

		setTimeout(pauseAudio, (question.answerTimings.end - question.answerTimings.start) * 1000);
	});

	onDestroy(() => {
		pauseAudio();
	});
</script>

<button class="box" on:click={goToTable}>
	<div>
		{question.artist}
		{#if question.name}
			—
		{/if}
	</div>
	<div>
		{#if question.name}
			{question.name}
		{/if}
	</div>
</button>

<style>
	button {
		padding: 5%;
		color: inherit;
		width: 80%;
		height: 70%;
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		line-height: 2em;
		font-size: 5vh;
		cursor: pointer;
		outline: none;
		border: none;
	}

	button > div:last-child {
		font-weight: 600;
	}
</style>
