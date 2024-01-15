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
	let disabledItems = [];

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
	async function handleRemoveTodo(e) {
		const id = e.detail.id;
		if (disabledItems.includes(id)) return;
		disabledItems = [...disabledItems, id];
		await fetch(`https://jsonplaceholder.typicode.com/todos/${id}`, {
			method: 'DELETE'
		}).then((response) => {
			if (response.ok) {
				todos = todos.filter((todo) => todo.id !== e.detail.id);
			} else {
				alert('An error occurred');
			}
		});
		disabledItems = disabledItems.filter((item) => item !== id);
	}
	async function handleToggleTodo(e) {
		const id = e.detail.id;
		const value = e.detail.value;
		if (disabledItems.includes(id)) return;
		disabledItems = [...disabledItems, id];
		await fetch(`https://jsonplaceholder.typicode.com/todos/${id}`, {
			method: 'PATCH',
			body: JSON.stringify({
				completed: value
			}),
			headers: { 'Content-type': 'application/json; charset=UTF-8' }
		}).then(async (response) => {
			if (response.ok) {
				const updatedTodo = await response.json();
				todos = todos.map((todo) => (todo.id === id ? updatedTodo : { ...todo }));
			} else {
				alert('An error occurred');
			}
		});
		disabledItems = disabledItems.filter((item) => item !== id);

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
			{disabledItems}
			disableAdding={isAdding}
			bind:this={todoList}
			on:addtodo={handleAddTodo}
			on:removetodo={handleRemoveTodo}
			on:toggletodo={handleToggleTodo}
			let:todo
			let:handleToggleTodo
			let:index
		>
			{@const { id, completed, title } = todo}
			{@const temp_index = index}
			<svelte:fragment slot="title">{temp_index + 1} - {temp_todo.title}</svelte:fragment>
			<!-- <Todo {todo} on:remove on:toggle> -->
			<div>
				<input
					disabled={disabledItems.includes(id)}
					on:input={(event) => {
						event.currentTarget.checked = completed;
						handleToggleTodo(id, !completed);
					}}
					type="checkbox"
					checked={completed}
				/>
				{title}
			</div>
		</TodoList>
	</div>
{/if}

<style lang="scss">
</style>
