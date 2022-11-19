<script>
	import { initializeApp } from 'firebase/app';
	import { getFirestore } from 'firebase/firestore';
	import { firebaseConfig } from '$lib/firebaseConfig';

	const app = initializeApp(firebaseConfig);
	const db = getFirestore();
	console.log(firebaseConfig, app, db);

	let todos = [
		{ task: 'Get milk', isComplete: false, createdAt: '2022-11-10' },
		{ task: 'Get eggs', isComplete: true, createdAt: '2022-11-10' },
		{ task: 'Get bread', isComplete: false, createdAt: '2022-11-10' }
	];
	let task = '';

	const addTodo = () => {
		let todo = {
			task: task,
			isComplete: false,
			createdAt: new Date()
		};
		if (task !== '') todos = [...todos, todo];
		task = '';
	};

	const markTodoAsComplete = (index) => {
		todos[index].isComplete = !todos[index].isComplete;
	};

	const deleteTodo = (index) => {
		todos.splice(index, 1);
		todos = todos;
	};

	const keyPressed = (e) => {
		if (e.key === 'Enter') addTodo();
	};
</script>

<input type="text" placeholder="Add a task" bind:value={task} />
<button on:click={addTodo}>Add</button>

<ul>
	{#each todos as todo, index}
		<li class:complete={todo.isComplete}>
			{index + 1}. {todo.task}
			<button on:click={() => markTodoAsComplete(index)}>
				<i class="fas fa-check" />
			</button>
			<button on:click={() => deleteTodo(index)}>
				<i class="fas fa-close" />
			</button>
		</li>
	{:else}
		<li>There is nothing to do</li>
	{/each}
</ul>

<svelte:window on:keydown={keyPressed} />

<style>
	.complete {
		text-decoration: line-through;
	}
</style>
