<script lang="ts">
	import { createEventDispatcher, onMount } from 'svelte';

	const dispatch = createEventDispatcher<{rating: number}>();
	let values = Array.from({length: 10}, (_, i) => i+1);

	let track: HTMLDivElement;
	let trackBall: HTMLButtonElement;

	let selected = 1;
	let trackerLeft = 0;
	let leftOffset = 0;

	onMount(() => {
		setTrackerLeft(selected);
	});

	let isSliding = false;
	const handleValueSelect = (val: number) => {
		selected = val;
		dispatch('rating', val);
	}

	const handleSlideValueSelect = (val: number) => {
		handleValueSelect(val);
		setTrackerLeft(val);
	}

	const handleTrackerMouseDown = (event: MouseEvent) => {
		isSliding = true;
		leftOffset = event.clientX - trackerLeft;
	}

	const handleTrackerMouseUp = () => {
		if (isSliding) {
			isSliding = false;
			setTrackerLeft(selected);
		}
	}

	const handleMouseMove = (event: MouseEvent) => {
		if (isSliding) {
			const newLeft = event.clientX - leftOffset;
			const left = Math.max(0, Math.min(newLeft, track.clientWidth - trackBall.offsetWidth));
			trackerLeft = left;

			const {width} = track.getBoundingClientRect?.() ?? {width: 0};
			const lengthOfEachValueHalfwayPoint = width / values.length / 3;
			const val = Math.ceil(((left + lengthOfEachValueHalfwayPoint) / width) * values.length);
			console.log(val);

			if (selected !== val) {
				handleValueSelect(val);
			}
		}
	}

	const handleKeyDown = (event: KeyboardEvent) => {
		if (event.key === 'ArrowLeft' || event.key === 'ArrowDown') {
			handleSlideValueSelect(Math.max(1, selected - 1));
		} else if (event.key === 'ArrowRight' || event.key === 'ArrowUp') {
			handleSlideValueSelect(Math.min(values.length, selected + 1));
		} else if (event.key === 'End') {
			handleSlideValueSelect(values.length);
		} else if (event.key === 'Home') {
			handleSlideValueSelect(1);
		} else if (event.key === 'PageUp') {
			handleSlideValueSelect(Math.min(values.length, selected + 3));
		} else if (event.key === 'PageDown') {
			handleSlideValueSelect(Math.max(1, selected - 3));
		}
	}

	const setTrackerLeft = (val: number) => {
		const {width} = track.getBoundingClientRect?.() ?? {width: 0};
		trackerLeft = (width / values.length) * (val - 1);
	}
</script>

<svelte:document on:mouseup={handleTrackerMouseUp} on:mousemove={handleMouseMove}/>

<div bind:this={track} class="relative flex isolate bg-white border border-black rounded-2xl items-center">
	<button
		role="slider"
		aria-valuenow={selected}
		aria-valuemin={1}
		aria-valuemax={values.length}
		bind:this={trackBall}
		class="absolute flex pb-0.5 focus:outline outline-2 outline-sky-400 cursor-grab font-bold items-center justify-center rounded-full z-10 bg-black border border-white size-6"
		style:left={`${trackerLeft}px`}
		class:transition-all={!isSliding}
		on:mousedown={handleTrackerMouseDown}
		on:keydown={handleKeyDown}
	>
		{selected}
	</button>
	{#each values as value}
		<button tabindex={-1} on:click={() => handleSlideValueSelect(value)} class:pointer-events-none={isSliding} class="flex cursor-pointer pb-0.5 items-center justify-center h-6 w-6 font-semibold text-black">{value}</button>
	{/each}
</div>