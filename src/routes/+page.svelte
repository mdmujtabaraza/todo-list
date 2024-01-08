<script lang="ts">
	import TodoList from '$lib/TodoList.svelte';
	import { v4 as uuidv4 } from 'uuid';
	import { tick } from 'svelte';

	let todoList;
	let showList = true;

	let todos = [
		{
			id: uuidv4(),
			title: 'Todo 1',
			completed: true
		},
		{
			id: uuidv4(),
			title: 'Todo 2',
			completed: false
		},
		{
			id: uuidv4(),
			title: 'Todo 3',
			completed: true
		}
	];

	async function handleAddTodo(e) {
		e.preventDefault();
		// console.log(e.detail.title);
		// todos.push({
		// 	id: uuidv4(),
		// 	title: e.detail.title,
		// 	completed: false
		// });
		// todos = todos;
		console.log(document.querySelectorAll('.todo-list ul li'));
		todos = [...todos, { id: uuidv4(), title: e.detail.title, completed: false }];
		await tick();
		console.log(document.querySelectorAll('.todo-list ul li'));
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
	<div style:max-width="200px">
		<TodoList
			{todos}
			bind:this={todoList}
			on:addtodo={handleAddTodo}
			on:removetodo={handleRemoveTodo}
			on:toggletodo={handleToggleTodo}
		/>
	</div>
{/if}

<style lang="scss">
</style>
