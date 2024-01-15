<script lang="ts">
	import TodoList from '$lib/TodoList.svelte';
	import { v4 as uuidv4 } from 'uuid';
	import { onMount, tick } from 'svelte';

	let todoList;
	let showList = true;

	let todos = null;
	let error = null;
	let isLoading = false;
	let isAdding = false;

	onMount(() => {
		loadTodos();
	});

	async function loadTodos() {
		isLoading = true;
		await fetch('https://jsonplaceholder.typicode.com/todos?_limit=10').then(async (response) => {
			if (response.ok) {
				todos = await response.json();
			} else {
				error = 'An error occurred';
			}
		});
		isLoading = false;
	}

	async function handleAddTodo(e) {
		e.preventDefault();
		isAdding = true;
		await fetch('https://jsonplaceholder.typicode.com/todos', {
			method: 'POST',
			body: JSON.stringify({
				title: e.detail.title,
				completed: false
			}),
			headers: { 'Content-type': 'application/json; charset=UTF-8' }
		}).then(async (response) => {
			if (response.ok) {
				const todo = await response.json();
				todos = [...todos, { ...todo, id: uuidv4() }];
				todoList.clearInput();
			} else {
				alert('An error occurred');
			}
		});
		isAdding = false;
		await tick();
		todoList.focusInput();
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
	<div style:max-width="400px">
		<TodoList
			{todos}
			{error}
			{isLoading}
			disableAdding={isAdding}
			bind:this={todoList}
			on:addtodo={handleAddTodo}
			on:removetodo={handleRemoveTodo}
			on:toggletodo={handleToggleTodo}
		/>
	</div>
{/if}

<style lang="scss">
</style>
