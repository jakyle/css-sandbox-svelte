<script lang="ts">

	let dragElement: HTMLButtonElement;
	let containerElement: HTMLDivElement;
	let isDragging = false;

	let top = 0;
	let left = 0;

	let topOffset = 0;
	let leftOffset = 0;

	$: leftValue = `${left}px`;
	$: topValue = `${top}px`;

	const handleMouseDown = (event: MouseEvent) => {
		isDragging = true;
		leftOffset = event.clientX - left;
		topOffset = event.clientY - top;
	} 

	const handleMouseMove = (event: MouseEvent) => {
		if (isDragging) {
			// const { left: containerLeft, top: containerTop } = containerElement.getBoundingClientRect();
			const newLeft = event.clientX  - leftOffset;
			const newTop = event.clientY  - topOffset;
			
			left = Math.max(0, Math.min(newLeft, containerElement.clientWidth - dragElement.offsetWidth));
			top = Math.max(0, Math.min(newTop, containerElement.clientHeight - dragElement.offsetHeight));
		}
	}

</script>

<svelte:document on:mouseup={() => isDragging = false} on:mousemove={handleMouseMove} />

<div bind:this={containerElement} class="size-1/2 bg-yellow-900 flex items-center justify-center relative">
	<button
		bind:this={dragElement}
		style:top={topValue}
		style:left={leftValue}
		on:mousedown={handleMouseDown}
		class:cursor-grab={!isDragging}
		class:cursor-grabbing={isDragging}
		class="size-24 rounded-xl bg-yellow-300 absolute" 
	/>
</div>