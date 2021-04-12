<script>
	import { onMount } from "svelte";
	import { tweened } from "svelte/motion";
	import { cubicOut } from "svelte/easing";
	


	let bpm_time;
	let srcNode;
	//https://drive.google.com/uc?export=download&id=1abrkBjjJ4MmaubjIBl-yWPi2Fw2qdW9B


	import AudioPlayer, { stopAll, playAll } from './AudioPlayer.svelte'

  	let audioTracks = [
		"./stems/32elseman/32elseman_P1_S0001/track1.wav",
		"./stems/32elseman/32elseman_P1_S0001/track2.wav",
		"./stems/32elseman/32elseman_P1_S0001/track3.wav",
		"./stems/32elseman/32elseman_P1_S0001/track4.wav",
		"./stems/32elseman/32elseman_P1_S0001/track5.wav",
		"./stems/32elseman/32elseman_P1_S0001/track6.wav",
		"./stems/32elseman/32elseman_P1_S0001/track7.wav",
		"./stems/32elseman/32elseman_P1_S0001/track8.wav"		
  ]


	onMount(() => {
		console.log("onMount ")
	
		console.log("mothefucks");

	});

	const progress = tweened(0, {
		duration: 1,
		easing: cubicOut,
	});


</script>

<main>
	<h1>Pattern time: {bpm_time}</h1>
	<p>
		Visit the <a href="https://svelte.dev/tutorial">Svelte tutorial</a> to learn
		how to build Svelte apps.
	</p>

	<progress value={$progress} />

	<button on:click={() => progress.set(0.5)}> 50% </button>

	<button on:click={() => progress.set(0.75)}> 75% </button>

	<button on:click={() => progress.set(1)}> 100% </button>
	<button on:click={playAll}>play all!</button>
	

	{#each audioTracks as src}
	<AudioPlayer {src} />
	{/each}

</main>

<style>
	main {
		text-align: center;
		padding: 1em;
		max-width: 240px;
		margin: 0 auto;
	}

	h1 {
		color: #ff3e00;
		text-transform: uppercase;
		font-size: 4em;
		font-weight: 100;
	}

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}
	progress {
		display: block;
		width: 100%;
	}
</style>
