<script lang="ts">
	import { browser, dev } from '$app/environment'
	import maxwell from '$lib/assets/maxwell.mp3'
	import todd from '$lib/assets/todd.webp'
	import { Howl } from 'howler'
	import { onMount } from 'svelte'

	let dialog: HTMLDialogElement
	let audio: Howl
	let spin = false
	let spinCount: number
	let clickCount: number
	let totalClickCount: number
	let volume: number

	onMount(() => {
		dialog.showModal()
		spinCount = parseInt(localStorage.getItem('spinCount') ?? '0')
		clickCount = parseInt(localStorage.getItem('clickCount') ?? '0')
		totalClickCount = parseInt(localStorage.getItem('totalClickCount') ?? '0')
		volume = parseInt(localStorage.getItem('volume') ?? '100')
	})

	$: if (browser && spinCount > 0) {
		localStorage.setItem('spinCount', String(spinCount))
	}
	$: if (browser && clickCount > 0) {
		localStorage.setItem('clickCount', String(clickCount))
	}
	$: if (browser && totalClickCount > 0) {
		localStorage.setItem('totalClickCount', String(totalClickCount))
	}

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
		if (browser && volume) localStorage.setItem('volume', String(volume))
		if (audio) audio.volume(volume / 100)
	}

	function click() {
		clickCount++
		totalClickCount++
	}

	function resetClicks() {
		clickCount = 0
		localStorage.removeItem('clickCount')
	}

	function resetAll() {
		spinCount = 0
		clickCount = 0
		totalClickCount = 0
		localStorage.removeItem('spinCount')
		localStorage.removeItem('clickCount')
		localStorage.removeItem('totalClickCount')
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
				<h1>{browser ? `The Todd has spun ${spinCount} times!` : '\xa0'}</h1>
				<p>
					{browser
						? `You've clicked ${totalClickCount} times (speed: ${playbackRate.toFixed(2)}x)`
						: '\xa0'}
				</p>
				<p>{browser && dev ? `(temp click count: ${clickCount})` : '\xa0'}</p>
			</hgroup>
		</header>

		<button
			class="todd seamless"
			style:--duration={spinDuration + 's'}
			on:click={click}
			on:animationiteration={() => spinCount++}
		>
			<img src={todd} alt="todd" draggable="false" />
		</button>

		<footer>
			<label
				>Volume
				<input type="range" bind:value={volume} min="0" max="100" />
			</label>
			<div class="grid">
				<button on:click={() => (spin = !spin)}>{spin ? 'Pause' : 'Play'}</button>
				<button on:click={resetClicks}>Reset Speed</button>
				<button on:click={resetAll}>Reset All</button>
			</div>
			<div class="credits">
				<div>Character © <a href="https://linktr.ee/toddrick" target="_blank">Toddrick</a></div>
				<div>Art © <a href="https://www.pulexart.com/" target="_blank">Pulex</a></div>
			</div>
		</footer>
	</div>
</main>

<style lang="scss">
	main {
		height: 100vh;
		overflow: hidden;
		-webkit-user-select: none;
		user-select: none;
		text-align: center;
	}

	dialog {
		-webkit-backdrop-filter: var(--modal-overlay-backdrop-filter);
		backdrop-filter: var(--modal-overlay-backdrop-filter);
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

	.container {
		position: relative;
		height: 100%;
		display: flex;
		flex-direction: column;
		justify-content: space-between;
	}

	header h1 {
		font-size: 1.5rem;
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

	.grid {
		grid-template-columns: repeat(auto-fit, minmax(0%, 1fr));
	}

	.credits {
		display: flex;
		justify-content: space-evenly;
	}
</style>
