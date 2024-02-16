<script lang="ts">
	import { createEventDispatcher } from 'svelte';
	const dispatch = createEventDispatcher<{rating: number}>();
	let stars = Array.from({length: 5}, (_, i) => ({ value: i+1, label: '★' })).reverse();
</script>

<div class="flex flex-row-reverse gap-3">
	{#each stars as {value, label}}
		<input on:change={() => dispatch('rating', value)} type="radio" id={value.toString()} name="rating" {value} class="hidden peer"/>
		<label class="cursor-pointer peer-checked:text-yellow-400 peer-hover:text-yellow-500 peer-hover:animate-spin" for={value.toString()}>{label}</label>
	{/each}
</div>