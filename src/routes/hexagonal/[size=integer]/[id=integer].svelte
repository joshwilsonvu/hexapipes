<script context="module">
	export async function load({ params, fetch }) {
		const size = `${params.size}x${params.size}`;
		const id = Number(params.id);
		const folderNum = Math.floor((id - 1) / 100);
		const url = `/_instances/hexagonal/${size}/${folderNum}/${id}.json`;
		const response = await fetch(url);

		if (response.ok) {
			const data = await response.json();
			return {
				status: response.status,
				props: {
					width: data.width,
					height: data.height,
					tiles: data.tiles
				}
			};
		} else {
			return {
				status: response.status
			};
		}
	}
</script>

<script>
	import { page } from '$app/stores';
	import PuzzleWrapper from '$lib/puzzleWrapper/PuzzleWrapper.svelte';
	import { puzzleCounts } from '$lib/stores';

	/** @type {Number} */
	export let width;
	/** @type {Number} */
	export let height;
	/** @type {Number[]} */
	export let tiles;
</script>

<svelte:head>
	<title>
		{$page.params.size}x{$page.params.size} Hexagonal Pipes Puzzle #{$page.params.id}
	</title>
</svelte:head>

<div class="info container">
	<h2>{$page.params.size}x{$page.params.size} Hexagonal Pipes Puzzle #{$page.params.id}</h2>

	<p>Rotate the tiles so that all pipes are connected with no loops.</p>
</div>

<PuzzleWrapper
	{width}
	{height}
	{tiles}
	category={'hexagonal'}
	size={Number($page.params.size)}
	puzzleId={Number($page.params.id)}
	puzzlesCount={$puzzleCounts.hexagonal[`${$page.params.size}x${$page.params.size}`]}
/>

<style>
	.info {
		text-align: center;
	}
</style>
