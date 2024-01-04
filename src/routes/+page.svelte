<script lang="ts">
	import TodoList from '$lib/TodoList.svelte';
	import { v4 as uuidv4 } from 'uuid';

	const todos = [
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
</script>

<TodoList {todos} />

<style lang="scss">
</style>
