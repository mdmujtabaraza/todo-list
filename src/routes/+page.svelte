<script lang="ts">
	import TodoList from '$lib/TodoList.svelte';
	import { v4 as uuidv4 } from 'uuid';

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
		// e.preventDefault();
		// console.log(e.detail.title);
		// todos.push({
		// 	id: uuidv4(),
		// 	title: e.detail.title,
		// 	completed: false
		// });
		// todos = todos;
		todos = [...todos, { id: uuidv4(), title: e.detail.title, completed: false }];
	}
</script>

<TodoList {todos} on:addtodo={handleAddTodo} />

<style lang="scss">
</style>
