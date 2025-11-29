<script lang="ts">
	import maxwell from '$lib/assets/maxwell.mp3'
	import todd from '$lib/assets/todd.webp'
	import { Howl } from 'howler'

	let dialog: HTMLDialogElement
	let spin = false
	let audio: Howl

	function play() {
		dialog.close()
		audio = new Howl({ src: maxwell, loop: true })
		audio.play()
		spin = true
	}
</script>

<main>
	<img class="todd" src={todd} alt="todd" draggable="false" class:spin />

	<dialog bind:this={dialog} on:click={play} on:keydown={(e) => e.key === 'Enter' && play()} open>
		Click to Start
	</dialog>
</main>

<style>
	:root {
		--modal-overlay-backdrop-filter: blur(1rem);
	}

	main {
		height: 100vh;
		display: grid;
		place-content: center;
	}

	.todd {
		width: 80vw;
		max-width: 512px;
	}

	.todd.spin {
		animation: 3s linear infinite spin;
	}

	@keyframes spin {
		to {
			transform: rotateZ(360deg);
		}
	}
</style>
