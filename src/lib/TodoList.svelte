<svelte:options immutable={true} />

<script lang="ts">
	import Layout from '../routes/+layout.svelte';
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
		if (autoScroll) listDiv.scrollTo(0, listDivScrollHeight);
		autoScroll = false;
	});

	export let todos = [];
	let prevTodos = todos;

	$: {
		// console.log(prevTodos, todos);
		autoScroll = todos.length > prevTodos.length;
		prevTodos = todos;
	}

	export function clearInput() {
		inputText = '';
	}
	export function focusInput() {
		input.focus();
	}

	let inputText = '';
	let input, listDiv, listDivScrollHeight;
	let autoScroll: boolean;

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
		<div bind:offsetHeight={listDivScrollHeight}>
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
	.todo-list {
		max-height: 150px;
		overflow: auto;
	}
</style>
