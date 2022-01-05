<script>
import { onMount } from "svelte";

	let todos = [];
	let toDoInput = "";

	function addToDo(newToDo) {
		todos = [...todos, newToDo];
		localStorage.setItem("todos", JSON.stringify(todos))
	}

	function removeToDo(id) {
		console.log("test");
		
		todos.splice(id, 1);
		todos = todos;
	}

	onMount(() => {
		todos = JSON.parse(localStorage.getItem("todos")) || [];
	})

</script>

<main>
	<h1>To Do App</h1>
	<form on:submit|preventDefault={addToDo(toDoInput)}>
		<input type="text" bind:value="{toDoInput}" placeholder="Write new todo here">
		<button type="submit" >Add</button>
	</form>

	<ul style="list-style-type: none;">
		{#each todos as todoItem, i}
		<button on:click|once="{removeToDo(i)}"><li>{todoItem}</li></button> <br>
		{/each}
	</ul>
	<button on:click|once="{removeToDo}"><li>test</li></button> <br>
</main>

<style>
	main {
		text-align: center;
		padding: 1em;
		max-width: 240px;
		margin: 0 auto;
	}

	h1 {
		color: #ff3e00;
		text-transform: uppercase;
		font-size: 4em;
		font-weight: 100;
	}

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}
</style>