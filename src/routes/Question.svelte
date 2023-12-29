<script lang="ts">
	import { onMount, onDestroy } from 'svelte';
	import { goto } from '$app/navigation';
	import { page } from '$app/stores';
	import config from './config.json';
	import TimeoutIndicator from './TimeoutIndicator.svelte';

	export let packIdx: number;
	export let questionInProgress: number[];

	$: question = config[packIdx].categories[questionInProgress[0]].songs[questionInProgress[1]];
	$: questionDuration = (question.questionTimings.end - question.questionTimings.start) * 1000;
	$: timerValue = question.questionTimings.end - question.questionTimings.start;

	let resetId = 1;
	let audio: HTMLAudioElement | null = null;
	let stopTimeout: ReturnType<typeof setTimeout> | null = null;
	let counterInterval: ReturnType<typeof setInterval> | null = null;

	function pauseAudio() {
		audio?.pause();

		if (stopTimeout) {
			clearTimeout(stopTimeout);
			stopTimeout = null;
		}

		if (counterInterval) {
			clearInterval(counterInterval);
			counterInterval = null;
		}
	}

	function playAudio() {
		if (!audio) return;
		audio.play();

		stopTimeout = setTimeout(() => {
			pauseAudio();
			timerValue = 0;
		}, (question.questionTimings.end - audio.currentTime) * 1000);

		counterInterval = setInterval(() => {
			timerValue -= 0.01;
		}, 10);
	}

	function resetAudio() {
		if (!audio) return;
		pauseAudio();
		audio.currentTime = question.questionTimings.start;
		timerValue = question.questionTimings.end - question.questionTimings.start;
		resetId = Math.random();
		playAudio();
	}

	function goNext() {
		const newQuery = new URLSearchParams($page.url.searchParams);
		newQuery.set('isAnswering', 'true');
		goto(`?${newQuery}`);
	}

	onMount(async () => {
		console.log('qwe')
		audio = new Audio(`/${question.filename}`);

		await new Promise((res) => {
			audio?.addEventListener('loadeddata', res);
		});

		resetAudio();
	});

	onDestroy(() => {
		pauseAudio();
	});
</script>

<section>
	<div class="two-buttons">
		<button class="box hover-zoom" on:click={resetAudio}>
			<svg style="width:24px;height:24px" viewBox="0 0 24 24">
				<path
					fill="currentColor"
					d="M17.65,6.35C16.2,4.9 14.21,4 12,4A8,8 0 0,0 4,12A8,8 0 0,0 12,20C15.73,20 18.84,17.45 19.73,14H17.65C16.83,16.33 14.61,18 12,18A6,6 0 0,1 6,12A6,6 0 0,1 12,6C13.66,6 15.14,6.69 16.22,7.78L13,11H20V4L17.65,6.35Z"
				/>
			</svg>
		</button>
		<button class="box hover-zoom" on:click={stopTimeout ? pauseAudio : playAudio}>
			{#if stopTimeout}
				<svg style="width:24px;height:24px" viewBox="0 0 24 24">
					<path fill="currentColor" d="M14,19H18V5H14M6,19H10V5H6V19Z" />
				</svg>
			{:else}
				<svg style="width:24px;height:24px" viewBox="0 0 24 24">
					<path fill="currentColor" d="M8,5.14V19.14L19,12.14L8,5.14Z" />
				</svg>
			{/if}
		</button>
	</div>
	<div class="timer-container box">
		{#key resetId}
			<TimeoutIndicator paused={!stopTimeout} strokeWidth={16} duration={questionDuration}>
				<div class="counter">
					{Math.ceil(timerValue)}
				</div>
			</TimeoutIndicator>
		{/key}
	</div>
	<button class="box hover-zoom" on:click={goNext}>
		<svg style="width:24px;height:24px" viewBox="0 0 24 24">
			<path fill="currentColor" d="M4,11V13H16L10.5,18.5L11.92,19.92L19.84,12L11.92,4.08L10.5,5.5L16,11H4Z" />
		</svg>
	</button>
</section>

<style>
	section {
		width: 70%;
		height: 70%;
		display: flex;
		justify-content: center;
		align-items: center;
		gap: 5%;
	}

	.timer-container {
		height: 60%;
		border-radius: 50%;
	}

	.counter {
		display: flex;
		justify-content: center;
		align-items: center;
		width: 100%;
		height: 100%;
		font-size: 8vh;
	}

	button {
		border-radius: 50%;
		aspect-ratio: 1;
		color: white;
		border: none;
		display: flex;
		align-items: center;
		justify-content: center;
		height: 10vh;
	}

	svg {
		transform: scale(2);
	}

	.two-buttons {
		display: flex;
		flex-direction: column;
		gap: 2vh;
	}
</style>
