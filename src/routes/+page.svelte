<script lang="ts">
	import { onMount } from 'svelte';
	import PriorityQueue from 'priorityqueue';

	class Lane {
		declare totalCount: number;
		declare itemList: number[];

		constructor() {
			this.totalCount = 0;
			this.itemList = [0];
		}
	}

	const maxNumericCompare = (a: number, b: number) => (a > b ? 1 : a < b ? -1 : 0);
	const minNumericCompare = (a: number, b: number) => (a < b ? 1 : a > b ? -1 : 0);
	const comparator = (a: Lane, b: Lane) => {
		return minNumericCompare(a.totalCount, b.totalCount);
	};

	let laneList: Lane[] = [];
	let laneQueue = new PriorityQueue<Lane>({ comparator });
	for (const _ of [...new Array(5).keys()]) {
		const lane = new Lane();
		laneList.push(lane);
		laneQueue.push(lane);
	}

	let inputItemCount: HTMLInputElement;

	function addPersonToLine() {
		const count = Number.parseInt(inputItemCount.value);
		if (count !== Number.NaN && count > 0) {
			const lane = laneQueue.pop();
			lane.itemList.push(count);
			lane.totalCount += count;
			laneQueue.push(lane);
		}
		laneList = laneList;
	}

	function remove1ItemFromEachLine() {
		for (const lane of laneQueue.collection) {
			if (lane.itemList.length === 1 && lane.itemList[0] === 0) continue;
			if (lane.itemList.length > 0) {
				if (lane.itemList[0] === 0) {
					lane.itemList.splice(0, 1);
				}
				lane.itemList[0]--;
				lane.totalCount--;
			}
		}
		laneList = laneList;
	}

	onMount(function () {
		setInterval(() => {
			remove1ItemFromEachLine();
		}, 500);
	});
</script>

<svelte:head>
	<title>Home</title>
	<meta name="description" content="Svelte demo app" />
</svelte:head>

<section>
	<div class="row">
		<label for="item-count">
			<input bind:this={inputItemCount} type="number" name="item-count" />
		</label>
		<button on:click={addPersonToLine}>Add to Line</button>
	</div>
	<div class="row">
		{#each laneList as lane}
			<div class="col">
				{#each lane.itemList as count}
					<div>{count}</div>
				{/each}
			</div>
		{/each}
	</div>
</section>

<style>
	.row {
		display: flex;
		flex-direction: row;
		gap: 10px;
	}
	.col {
		display: flex;
		flex-direction: column;
		gap: 10px;
	}

	section {
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		flex: 0.6;
	}
</style>
