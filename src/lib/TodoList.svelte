<svelte:options immutable={true} />

<script lang="ts">
	import Layout from '../routes/+layout.svelte';
	import Button from './Button.svelte';
	import { createEventDispatcher, afterUpdate } from 'svelte';
	import Icon from '@iconify/svelte';

	afterUpdate(() => {
		// console.log(listDiv.offsetHeight);
		if (autoScroll) listDiv.scrollTo(0, listDivScrollHeight);
		autoScroll = false;
	});

	let inputText = '';
	let input, listDiv, listDivScrollHeight;
	let autoScroll: boolean;
	const dispatch = createEventDispatcher();

	export let todos = null;
	export let error = null;
	export let isLoading = false;
	export let disableAdding = false;
	export let disabledItems = [];
	let prevTodos = todos;

	$: {
		// console.log(prevTodos, todos);
		autoScroll = todos && prevTodos && todos.length > prevTodos.length;
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
	{#if isLoading}
		<p class="state-text">Loading...</p>
	{:else if error}
		<p class="state-text">{error}</p>
	{:else if todos}
		<div class="todo-list" bind:this={listDiv}>
			<div bind:offsetHeight={listDivScrollHeight}>
				{#if todos.length === 0}
					<p class="state-text">No todos yet</p>
				{:else}
					<ul>
						{#each todos as todo, index (todo.id)}
							{@const { id, title, completed } = todo}
							<li>
								<slot {todo} {index} {handleToggleTodo}>
									<div class:completed>
										<label>
											<input
												disabled={disabledItems.includes(id)}
												on:input={(event) => {
													event.currentTarget.checked = completed;
													handleToggleTodo(id, !completed);
												}}
												type="checkbox"
												checked={completed}
											/><slot name="title">{title}</slot>
										</label>
										<button
											disabled={disabledItems.includes(id)}
											class="remove-todo-button"
											aria-label="Remove todo: {title}"
											on:click={() => handleRemoveTodo(id)}
											><Icon icon="line-md:close-circle-twotone" color="#bd1414" /></button
										>
									</div>
								</slot>
							</li>
						{/each}
					</ul>
				{/if}
			</div>
		</div>
	{/if}
	<form class="add-todo-form" on:submit|preventDefault={handleAddTodo}>
		<input
			disabled={disableAdding || !todos}
			bind:this={input}
			bind:value={inputText}
			placeholder="New todo"
		/>
		<Button class="add-todo-button" type="submit" disabled={!inputText || disableAdding || !todos}
			>Add</Button
		>
	</form>
</div>

<style lang="scss">
	input {
		color: #000;
	}
	.todo-list-wrapper {
		background-color: #424242;
		border: 1px solid #4b4b4b;
		.state-text {
			// margin: 0;
			padding: 15px;
			text-align: center;
		}
		.todo-list {
			max-height: 200px;
			overflow: auto;
			ul {
				// margin: 0;
				// list-style: none;
				padding: 10px;
				li > div {
					margin-bottom: 5px;
					display: flex;
					align-items: center;
					background-color: #303030;
					border-radius: 5px;
					padding: 10px;
					position: relative;
					label {
						cursor: pointer;
						font-size: 18px;
						display: flex;
						align-items: baseline;
						padding-right: 20px;
						input[type='checkbox'] {
							margin: 0 10px 0 0;
							cursor: pointer;
							&:disabled {
								cursor: not-allowed;
							}
						}
					}
					&.completed > label {
						opacity: 0.5;
						text-decoration: line-through;
					}
					.remove-todo-button {
						padding: 5px;
						position: absolute;
						right: 10px;
						display: none;
						&:disabled {
							opacity: 0.4;
							cursor: not-allowed;
						}
					}
					&:hover {
						.remove-todo-button {
							display: block;
						}
					}
				}
			}
		}
		.add-todo-form {
			padding: 15px;
			background-color: #303030;
			display: flex;
			border-top: 1px solid #4b4b4b;
			flex-wrap: wrap;
			// :global(.add-todo-button) {
			// 	background-color: aqua;
			// }
			input {
				flex: 1;
				background-color: #424242;
				border: 1px solid #4b4b4b;
				padding: 10px;
				color: #fff;
				border-radius: 5px;
				margin-right: 10px;
			}
		}
	}
</style>
