<script lang="ts">
	import maxwell from '$lib/assets/maxwell.mp3'
	import todd from '$lib/assets/todd.webp'
	import { Howl } from 'howler'
	import { onMount } from 'svelte'

	let dialog: HTMLDialogElement
	let audio: Howl
	let spin = false
	let spinCount = 0
	let clickCount = 0
	let playbackRate = 0

	onMount(() => dialog.showModal())

	$: if (spin) {
		dialog.close()
		if (!audio) audio = new Howl({ src: maxwell, loop: true })
		audio.play()
	} else {
		if (audio) audio.pause()
	}

	$: {
		playbackRate = clickCount * 0.002 + 1
		if (audio) audio.rate(playbackRate)
	}
</script>

<main class:spin>
	<dialog bind:this={dialog}>
		<button class="seamless" on:click={() => (spin = true)}>Click to Start</button>
	</dialog>

	<div class="background" />

	<div class="container">
		<header>
			<h1>The Todd has spun {spinCount} times</h1>
		</header>

		<button
			class="todd seamless"
			on:click={() => clickCount++}
			on:animationiteration={() => spinCount++}
		>
			<img src={todd} alt="todd" draggable="false" />
		</button>

		<footer>
			<div class="grid">
				<button on:click={() => (spin = !spin)}>{spin ? 'Pause' : 'Play'}</button>
				<button on:click={() => (clickCount = 0)}>Reset Speed ({playbackRate.toFixed(2)}x)</button>
			</div>
			<div class="credits">
				<div>Character © <a href="https://linktr.ee/toddrick">Toddrick</a></div>
				<div>Art © <a href="https://www.pulexart.com/">Pulex</a></div>
			</div>
		</footer>
	</div>
</main>

<style lang="scss">
	:root {
		--modal-overlay-backdrop-filter: blur(1rem);
	}

	main {
		height: 100vh;
		overflow: hidden;
		-webkit-user-select: none;
		user-select: none;
	}

	dialog {
		-webkit-backdrop-filter: var(--modal-overlay-backdrop-filter);
	}

	button {
		width: auto;

		:is(&.seamless) {
			padding: 0;
			border: none;
			background: none;

			&:focus:not(:focus-visible) {
				box-shadow: none;
			}
		}
	}

	dialog button {
		width: 100%;
		height: 100%;

		&:focus-visible {
			/* this is the only focusable element in the dialog so it doesn't need a focus ring */
			box-shadow: none;
		}
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

	.todd {
		align-self: center;
		width: 80vw;
		max-width: 512px;
		animation: 3s linear infinite paused spin;
		transition: scale 0.1s;

		&:hover {
			scale: 1.05;
		}

		&:active {
			scale: 1;
		}

		.spin & {
			animation-play-state: running;
		}

		@keyframes spin {
			to {
				transform: rotateZ(360deg);
			}
		}

		img {
			pointer-events: none;
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

	.grid {
		grid-template-columns: repeat(auto-fit, minmax(0%, 1fr));
	}

	.credits {
		display: flex;
		justify-content: space-evenly;
	}
</style>
