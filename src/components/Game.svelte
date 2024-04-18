<script>
	import { onMount } from 'svelte';
	import { writable } from 'svelte/store';
	import Fleur from '../routes/fleur.jpg';

	const moles = Array.from({ length: 9 }, () => ({ visible: false, hit: false }));
	const score = writable(0);
	const timeLeft = writable(30);
	let interval;

	function startGame() {
		timeLeft.set(30);
		score.set(0);
		resetMoles();
		interval = setInterval(() => {
			timeLeft.update((n) => {
				if (n === 0) clearInterval(interval);
				return n - 1;
			});
			resetMoles();
		}, 1000);
	}

	function resetMoles() {
		moles.forEach((mole) => (mole.visible = false));
		let randomIndex = Math.floor(Math.random() * moles.length);
		moles[randomIndex].visible = true;
	}

	function whack(index) {
		if (moles[index].visible) {
			score.update((n) => n + 1);
			moles[index].hit = true;
			setTimeout(() => {
				moles[index].visible = false;
				moles[index].hit = false;
			}, 1000);
		}
	}

	onMount(() => {
		startGame();
	});
</script>

<h1 id="game-title">Whack-a-Mole</h1>
<span>
	<p>Time left: <span aria-live="assertive">{$timeLeft}</span></p>
	<p>Score: <span aria-live="assertive">{$score}</span></p>
</span>

<div aria-labelledby="game-title" aria-live="polite">
	{#each moles as mole, index}
		<div class="mole-container">
			<div class="mole-background"></div>
			{#if mole.visible}
				<img
					src={Fleur}
					class="mole {mole.hit ? 'hit' : ''}"
					on:click={() => whack(index)}
					alt="Mole"
				/>
			{/if}
		</div>
	{/each}
</div>

<style>
	h1 {
		text-align: center;
		font-size: 2.5em;
	}

	p {
		font-size: 1.5em;
	}
	div {
		display: grid;
		grid-template-columns: repeat(3, 1fr);
		width: 800px;
		margin: auto;
	}

	.mole-container {
		position: relative;
		width: 200px;
		height: 200px;
		margin: 10px;
	}

	.mole-background {
		position: absolute;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
		background-color: black;
		opacity: 1;
		transition: opacity 0.5s ease-in-out;
	}

	.mole {
		position: absolute;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
		opacity: 1;
		transition: opacity 0.5s ease-in-out;
		cursor: pointer;
	}

	.mole.hit {
		opacity: 0.5;
	}
</style>
