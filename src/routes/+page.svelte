<script lang="ts">
	import TodoList from '$lib/TodoList.svelte';
	import { v4 as uuidv4 } from 'uuid';
	import { tick } from 'svelte';

	let todoList;
	let showList = true;

	let todos = null;
	let promise = loadTodos();

	function loadTodos() {
		return fetch('https://jsonplaceholder.typicode.com/todos?_limit=10').then((response) => {
			if (response.ok) {
				return response.json();
			} else {
				throw new Error('An error has occured.');
			}
		});
	}

	async function handleAddTodo(e) {
		e.preventDefault();
		todos = [...todos, { id: uuidv4(), title: e.detail.title, completed: false }];
		await tick();
		todoList.clearInput();
	}
	function handleRemoveTodo(e) {
		todos = todos.filter((todo) => todo.id !== e.detail.id);
	}
	function handleToggleTodo(e) {
		todos = todos.map((todo) =>
			todo.id === e.detail.id ? { ...todo, completed: e.detail.value } : { ...todo }
		);
		// todos = todos.map((todo) => (todo.id === e.detail.id ? { ...todo, completed: !todo.completed
		// } : todo));
	}
</script>

<label>
	<input type="checkbox" bind:checked={showList} />
	Show/Hide list
</label>
{#if showList}
	{#await promise}
		<p>Loading...</p>
	{:then todos}
		<div style:max-width="400px">
			<TodoList
				{todos}
				bind:this={todoList}
				on:addtodo={handleAddTodo}
				on:removetodo={handleRemoveTodo}
				on:toggletodo={handleToggleTodo}
			/>
		</div>
	{:catch error}
		<p>{error.message || 'An error has occurred'}</p>
	{/await}
	<button
		on:click={() => {
			promise = loadTodos();
		}}>Refresh</button
	>
{/if}

<style lang="scss">
</style>
