<script context="module">
	/** Format duration in ms into "_d hh:mm:ss.sss"
	 * @param {Number} time - duration in ms
	 * @param {Boolean} includeMs - whether to include milliseconds in output
	 * @returns {String}
	 */
	export function formatTime(time, includeMs = true) {
		if (!Number.isFinite(time)) {
			return '--:--';
		}
		let result = '';
		const days = Math.floor(time / (24 * 3600 * 1000));
		if (days > 0) {
			result += `${days}d `;
		}
		const timeStr = new Date(time).toISOString().substring(11, includeMs ? 23 : 19);
		result += timeStr.replace('00:', '');
		return result;
	}
</script>

<script>
	import { settings } from '$lib/stores';
	export let solve = {
		puzzleId: -1,
		startedAt: -1,
		finishedAt: -1,
		elapsedTime: -1,
		error: null
	};

	/**
	 * @type {NodeJS.Timer}
	 */
	let timerId;
	let elapsed = 0;

	function toggleTimer(solve) {
		clearInterval(timerId);
		if (solve.startedAt === -1) {
			return;
		}
		if (solve.elapsedTime === -1) {
			elapsed = new Date().valueOf() - solve.startedAt;
			timerId = setInterval(() => {
				elapsed = new Date().valueOf() - solve.startedAt;
			}, 1000);
		} else {
			clearInterval(timerId);
			elapsed = solve.elapsedTime;
		}
	}

	$: toggleTimer(solve);
</script>

<div class="timer">
	{#if solve.error}
		{solve.error}
	{:else if solve.startedAt === -1}
		Loading puzzle...
	{:else if $settings.showTimer}
		{#if solve.finishedAt === -1}
			Time: {formatTime(elapsed, false)} 
		{:else}
			You have solved the puzzle in {formatTime(elapsed, true)}
		{/if}
	{/if}
</div>

<style>
	.timer {
		text-align: center;
		color: var(--text-color);
	}
</style>