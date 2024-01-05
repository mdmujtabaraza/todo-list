<script lang="ts">
	import Button from './Button.svelte';
	import { v4 as uuidv4 } from 'uuid';

	export let todos = [];
	let inputText = '';

	function handleAddTodo() {
		if (!inputText) return;
		todos = [...todos, { id: uuidv4(), title: inputText, completed: false }];
		inputText = '';
	}
</script>

<div class="todo-list-wrapper">
	<ul>
		{#each todos as { id, title }, index (id)}
			{@const number = index + 1}
			<li>{number} - {title}</li>
		{/each}
	</ul>
	<form class="add-todo-form" on:submit|preventDefault={handleAddTodo}>
		<input bind:value={inputText} />
		<Button type="submit" disabled={!inputText}>Add</Button>
	</form>
</div>

<style lang="scss">
	input {
		color: #000;
	}
</style>
