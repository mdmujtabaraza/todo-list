<script lang="ts">
	import TodoList from '$lib/TodoList.svelte';
	import { v4 as uuidv4 } from 'uuid';

	let todoList;
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

	function handleAddTodo(e) {
		e.preventDefault();
		// console.log(e.detail.title);
		// todos.push({
		// 	id: uuidv4(),
		// 	title: e.detail.title,
		// 	completed: false
		// });
		// todos = todos;
		setTimeout(() => {
			todos = [...todos, { id: uuidv4(), title: e.detail.title, completed: false }];
			todoList.clearInput();
		}, 1000);
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

	$: console.log(todos);
</script>

<TodoList
	{todos}
	bind:this={todoList}
	on:addtodo={handleAddTodo}
	on:removetodo={handleRemoveTodo}
	on:toggletodo={handleToggleTodo}
/>

<button
	on:click={() => {
		todoList.focusInput();
	}}>Focus Input</button
>

<style lang="scss">
</style>
