<svelte:options immutable={true} />

<script lang="ts">
	import Layout from '../routes/+layout.svelte';
	import Button from './Button.svelte';
	import { createEventDispatcher, afterUpdate } from 'svelte';
	import Icon from '@iconify/svelte';

	afterUpdate(() => {
		console.log(listDiv.offsetHeight);
		if (autoScroll) listDiv.scrollTo(0, listDivScrollHeight);
		autoScroll = false;
	});

	let inputText = '';
	let input, listDiv, listDivScrollHeight;
	let autoScroll: boolean;
	const dispatch = createEventDispatcher();

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
			{#if todos.length === 0}
				<p class="no-items-text">No todos yet</p>
			{:else}
				<ul>
					{#each todos as { id, title, completed } (id)}
						<li class:completed>
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
							<button aria-label="Remove todo: {title}" on:click={() => handleRemoveTodo(id)}
								><Icon icon="line-md:remove" /></button
							>
						</li>
					{/each}
				</ul>
			{/if}
		</div>
	</div>
	<form class="add-todo-form" on:submit|preventDefault={handleAddTodo}>
		<input bind:this={input} bind:value={inputText} placeholder="New todo" />
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
