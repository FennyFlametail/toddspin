<script lang="ts">
	import maxwell from '$lib/assets/maxwell.mp3'
	import todd from '$lib/assets/todd.webp'
	import { Howl } from 'howler'

	let image: HTMLImageElement
	let dialog: HTMLDialogElement
	let spin = false
	let audio: Howl

	$: if (spin) {
		dialog.close()
		if (!audio) audio = new Howl({ src: maxwell, loop: true })
		audio.play()
	} else {
		if (audio) audio.pause()
	}
</script>

<main class:spin>
	<div class="background" />

	<img bind:this={image} class="todd" src={todd} alt="todd" draggable="false" />

	<div class="controls">
		<button class="pauseButton" on:click={() => (spin = !spin)}>{spin ? 'Pause' : 'Play'}</button>
		<div class="credits">
			<div>Character © <a href="https://linktr.ee/toddrick">Toddrick</a></div>
			<div>Art © <a href="https://www.pulexart.com/">Pulex</a></div>
		</div>
	</div>

	<dialog
		bind:this={dialog}
		on:click={() => (spin = true)}
		on:keydown={(e) => e.key === 'Enter' && (spin = true)}
		open
	>
		Click to Start
	</dialog>
</main>

<style>
	:root {
		--modal-overlay-backdrop-filter: blur(1rem);
	}

	main {
		height: 100vh;
		position: relative;
		overflow: hidden;
		display: grid;
		place-content: center;
	}

	.background {
		position: absolute;
		height: 100%;
		width: 100%;
		background-image: url($lib/assets/background.jpg);
		background-size: auto 100%;
		background-position: center;
		animation: 10s linear infinite alternate paused backgroundZoom;
	}

	.spin .background {
		animation-play-state: running;
	}

	@keyframes backgroundZoom {
		to {
			transform: scale(1.1);
		}
	}

	.todd {
		width: 80vw;
		max-width: 512px;
		animation: 3s linear infinite paused spin;
	}

	.spin .todd {
		animation-play-state: running;
	}

	@keyframes spin {
		to {
			transform: rotateZ(360deg);
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

	.credits {
		display: flex;
		justify-content: space-evenly;
		text-align: center;
		margin-bottom: var(--spacing);
	}
</style>
