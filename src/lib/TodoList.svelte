<svelte:options immutable={true} />

<script lang="ts">
	import Button from './Button.svelte';
	import { createEventDispatcher, onMount, onDestroy, beforeUpdate, afterUpdate } from 'svelte';

	onMount(() => {
		console.log('Component mounted');
		return () => {
			console.log('Component destroyed in onMount');
		};
	});
	onDestroy(() => {
		console.log('Component destroyed');
	});
	beforeUpdate(() => {
		if (listDiv) {
			console.log(listDiv.offsetHeight);
		}
	});
	afterUpdate(() => {
		console.log(listDiv.offsetHeight);
	});

	export let todos = [];
	export const readOnly = 'read only';
	export function clearInput() {
		inputText = '';
	}
	export function focusInput() {
		input.focus();
	}

	let inputText = '';
	let input, listDiv;

	const dispatch = createEventDispatcher();

	function handleAddTodo() {
		const isNotCancelled = dispatch('addtodo', { title: inputText }, { cancelable: true });
		if (isNotCancelled) {
			inputText = '';
		}
	}

	function handleRemoveTodo(id) {
		dispatch('removetodo', { id });
	}

	function handleToggleTodo(id, value) {
		dispatch('toggletodo', { id, value });
	}
</script>

<div class="todo-list-wrapper">
	<div class="todo-list" bind:this={listDiv}>
		<ul>
			{#each todos as { id, title, completed } (id)}
				<!-- {(console.log(title), '')} -->
				<!-- {@debug id, title} -->
				<li>
					<label>
						<input
							on:input={(event) => {
								event.currentTarget.checked = completed;
								handleToggleTodo(id, !completed);
							}}
							type="checkbox"
							checked={completed}
						/>{title}
					</label>
					<button on:click={() => handleRemoveTodo(id)}>Remove</button>
				</li>
			{/each}
		</ul>
	</div>
	<form class="add-todo-form" on:submit|preventDefault={handleAddTodo}>
		<input bind:this={input} bind:value={inputText} />
		<Button type="submit" disabled={!inputText}>Add</Button>
	</form>
</div>

<style lang="scss">
	input {
		color: #000;
	}
</style>
