<script>
	import { fade } from 'svelte/transition';
	import { initializeApp } from 'firebase/app';
	import {
		getFirestore,
		collection,
		query,
		onSnapshot,
		doc,
		updateDoc,
		addDoc,
		Timestamp,
		orderBy,
		deleteDoc
	} from 'firebase/firestore';
	import { firebaseConfig } from '$lib/firebaseConfig';

	const app = initializeApp(firebaseConfig);
	const db = getFirestore(app);

	// Read
	let todos = [];
	const unsubscribe = onSnapshot(
		query(collection(db, 'todos'), orderBy('createdAt', 'asc')),
		(col) => {
			let fbTodos = [];
			col.forEach((doc) => {
				fbTodos = [...fbTodos, { id: doc.id, ...doc.data() }];
			});
			todos = fbTodos;
		}
	);

	// Create
	let task = '';
	const addTodo = async () => {
		await addDoc(collection(db, 'todos'), {
			task: task,
			isComplete: false,
			createdAt: Timestamp.fromDate(new Date())
		});
		task = '';
	};

	// Update
	const checkTodo = async (todo) => {
		await updateDoc(doc(db, 'todos', todo.id), {
			isComplete: !todo.isComplete
		});
	};

	// Delete
	const deleteTodo = async (todo) => {
		await deleteDoc(doc(db, 'todos', todo.id));
	};

	const keyPressed = (e) => {
		if (e.key === 'Enter') addTodo();
	};
</script>

<div class="w-full h-screen flex flex-col gap-3">
	<div class="mx-auto">THIS IS LOGO</div>
	<div class="flex w-72 mx-auto bg-slate-200  rounded-md">
		<input class="bg-slate-200 p-4" type="text" placeholder="Add a task" bind:value={task} />
		<button class="inline w-full" on:click={addTodo}>Add</button>
	</div>

	<ul class="w-72 h-full mx-auto my-2 overflow-y-auto">
		{#each todos as todo}
			<li
				transition:fade
				class:complete={todo.isComplete}
				class="flex justify-between rounded-md bg-slate-200 mb-1 px-2 py-1 transition duration-200"
			>
				<span>{todo.task}</span>

				<div>
					<button on:click={() => checkTodo(todo)}>
						<i class="fas fa-check" />
					</button>

					<button on:click={() => deleteTodo(todo)}>
						<i class="fas fa-trash" />
					</button>
				</div>
			</li>
		{:else}
			<li>There is nothing to do</li>
		{/each}
	</ul>
</div>

<svelte:window on:keypress={keyPressed} />

<style>
	.complete {
		background-color: green;
	}
</style>
