<script lang="ts">
	import maxwell from '$lib/assets/maxwell.mp3'
	import todd from '$lib/assets/todd.webp'
	import { Howl } from 'howler'

	let image: HTMLImageElement
	let dialog: HTMLDialogElement
	let spin = false
	let pausedDegrees = 0
	let audio: Howl

	function play() {
		dialog.close()
		if (!audio) audio = new Howl({ src: maxwell, loop: true })
		audio.play()
		spin = true
	}

	function pause() {
		audio.pause()
		pausedDegrees = parseFloat(getComputedStyle(image).rotate) % 360
		spin = false
	}
</script>

<main>
	<img
		bind:this={image}
		class="todd"
		src={todd}
		alt="todd"
		draggable="false"
		class:spin
		style:--degrees={`${pausedDegrees}deg`}
	/>

	<div class="controls">
		<button class="pauseButton" on:click={spin ? pause : play}>{spin ? 'Pause' : 'Play'}</button>
	</div>

	<dialog bind:this={dialog} on:click={play} on:keydown={(e) => e.key === 'Enter' && play()} open>
		Click to Start
	</dialog>
</main>

<style>
	:root {
		--modal-overlay-backdrop-filter: blur(1rem);
		background-image: url($lib/assets/background.jpg);
		background-size: auto 100%;
		background-position: center;
	}

	main {
		height: 100vh;
		display: grid;
		place-content: center;
	}

	.todd {
		width: 80vw;
		max-width: 512px;
		rotate: var(--degrees);
	}

	.todd.spin {
		animation: 3s linear infinite spin;
	}

	@keyframes spin {
		from {
			rotate: var(--degrees);
		}
		to {
			rotate: calc(var(--degrees) + 360deg);
		}
	}

	.controls {
		position: fixed;
		bottom: 0;
		left: 50%;
		transform: translateX(-50%);
		width: 100%;
		max-width: 512px;
		padding: 0 20px;
	}
</style>
