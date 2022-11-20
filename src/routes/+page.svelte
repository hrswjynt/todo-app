<script>
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

<input type="text" placeholder="Add a task" bind:value={task} />
<button on:click={addTodo}>Add</button>

<ul>
	{#each todos as todo}
		<li class:complete={todo.isComplete}>
			{todo.task}

			<button on:click={() => checkTodo(todo)}>
				<i class="fas fa-check" />
			</button>

			<button on:click={() => deleteTodo(todo)}>
				<i class="fas fa-close" />
			</button>
		</li>
	{:else}
		<li>There is nothing to do</li>
	{/each}
</ul>

<svelte:window on:keypress={keyPressed} />

<style>
	.complete {
		text-decoration: line-through;
	}
</style>
