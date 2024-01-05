<script lang="ts">
	import Button from './Button.svelte';

	export let todos = [];
	let inputText = '';

	function handleAddTodo() {}
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
		{inputText}
		<Button type="submit">Add</Button>
	</form>
</div>
