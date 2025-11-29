<script lang="ts">
	import { browser } from '$app/environment'
	import maxwell from '$lib/assets/maxwell.mp3'
	import todd from '$lib/assets/todd.webp'
	import { Howl } from 'howler'
	import { onMount } from 'svelte'

	let dialog: HTMLDialogElement
	let audio: Howl
	let spin = false
	let spinCount = 0
	let clickCount = 0

	onMount(() => {
		spinCount = parseInt(localStorage.getItem('spinCount') ?? '0')
		clickCount = parseInt(localStorage.getItem('clickCount') ?? '0')
		dialog.showModal()
	})

	$: if (spin) {
		dialog.close()
		if (!audio) audio = new Howl({ src: maxwell, loop: true })
		audio.play()
	} else {
		if (audio) audio.pause()
	}

	let playbackRate: number, spinDuration: number
	$: {
		playbackRate = clickCount * 0.002 + 1
		if (audio) audio.rate(playbackRate)
		spinDuration = 10 - clickCount / 50
	}

	$: {
		if (browser) {
			if (spinCount) localStorage.setItem('spinCount', String(spinCount))
			if (clickCount) localStorage.setItem('clickCount', String(clickCount))
		}
	}
</script>

<main class:spin>
	<dialog bind:this={dialog}>
		<button class="seamless" on:click={() => (spin = true)}>Click to Start</button>
	</dialog>

	<div class="background" />

	<div class="container">
		<header>
			<hgroup>
				<h1>{browser ? `The Todd has spun ${spinCount} times` : '\xa0'}</h1>
				<p>{browser ? `You've clicked ${clickCount} times` : '\xa0'}</p>
			</hgroup>
		</header>

		<button
			class="todd seamless"
			style:--duration={spinDuration + 's'}
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
				<div>Character © <a href="https://linktr.ee/toddrick" target="_blank">Toddrick</a></div>
				<div>Art © <a href="https://www.pulexart.com/" target="_blank">Pulex</a></div>
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
		animation: max(var(--duration), 0.00001s) linear infinite paused spin;
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
