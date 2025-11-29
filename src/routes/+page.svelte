<script lang="ts">
	import maxwell from '$lib/assets/maxwell.mp3'
	import todd from '$lib/assets/todd.webp'
	import { Howl } from 'howler'

	let dialog: HTMLDialogElement
	let spin = false
	let spinCount = 0
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

	<div class="image-container">
		<img
			class="todd"
			src={todd}
			alt="todd"
			draggable="false"
			on:animationiteration={() => spinCount++}
		/>
	</div>

	<div class="container">
		<header>
			<h1>The Todd has spun {spinCount} times</h1>
		</header>

		<footer>
			<button class="pauseButton" on:click={() => (spin = !spin)}>{spin ? 'Pause' : 'Play'}</button>
			<div class="credits">
				<div>Character © <a href="https://linktr.ee/toddrick">Toddrick</a></div>
				<div>Art © <a href="https://www.pulexart.com/">Pulex</a></div>
			</div>
		</footer>
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

<style lang="scss">
	:root {
		--modal-overlay-backdrop-filter: blur(1rem);
	}

	main {
		height: 100vh;
		overflow: hidden;
	}

	.background {
		position: fixed;
		top: 0;
		left: 0;
		height: 100%;
		width: 100%;
		background-image: url('$lib/assets/background.jpg');
		background-size: auto 100%;
		background-position: center;
		animation: 10s linear infinite alternate paused backgroundZoom;

		.spin & {
			animation-play-state: running;
		}

		@keyframes backgroundZoom {
			to {
				transform: scale(1.1);
			}
		}
	}

	.image-container {
		position: fixed;
		left: 0;
		top: 0;
		width: 100%;
		height: 100%;
		display: grid;
		place-content: center;
	}

	.todd {
		width: 80vw;
		max-width: 512px;
		animation: 3s linear infinite paused spin;

		.spin & {
			animation-play-state: running;
		}

		@keyframes spin {
			to {
				transform: rotateZ(360deg);
			}
		}
	}

	.container {
		position: relative;
		height: 100%;
		display: flex;
		flex-direction: column;
		justify-content: space-between;
		text-align: center;
	}

	.credits {
		display: flex;
		justify-content: space-evenly;
	}
</style>
