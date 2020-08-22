<script>
	import { quintOut } from 'svelte/easing';
	import { crossfade } from 'svelte/transition';
	import { flip } from 'svelte/animate';
	import "../node_modules/bootstrap/dist/css/bootstrap.min.css";

const [send, receive] = crossfade({
	duration: d => Math.sqrt(d * 200),

	fallback(node, params) {
		const style = getComputedStyle(node);
		const transform = style.transform === 'none' ? '' : style.transform;

		return {
			duration: 600,
			easing: quintOut,
			css: t => `
				transform: ${transform} scale(${t});
				opacity: ${t}
			`
		};
	}
});

let uid = 1;

let todos = [
	{ id: uid++, done: false, description: 'jogging' },
	{ id: uid++, done: false, description: 'take a bath' },
	{ id: uid++, done: false,  description: 'breakfast' },
	{ id: uid++, done: false, description: 'learn new thing' },
	{ id: uid++, done: false, description: 'a short break' },
	{ id: uid++, done: false, description: 'continue learning more thing' },
];

let emptyDone = true;

function add(input) {
	const todo = {
		id: uid++,
		done: false,
		description: input.value
	};

	todos = [todo, ...todos];
	input.value = '';
}

function remove(todo) {
	todos = todos.filter(t => t !== todo);
}

function mark(todo, done) {
	todo.done = done;
	remove(todo);
	todos = todos.concat(todo);
}
</script>

<div class="container text-center">
	<h1>To Do List App</h1>
	<input
		placeholder="Type and Enter to Add"
		on:keydown={e => e.key === 'Enter' && add(e.target)}
	>

	<div class="row text-left">
		<div class="col-md-6 col-sm-12 col-xs-12">
			<h2>To Do</h2>
			{#each todos.filter(t => !t.done) as todo (todo.id)}
				<label
					in:receive="{{key: todo.id}}"
					out:send="{{key: todo.id}}"
					animate:flip
				>
					<input type=checkbox on:change={() => mark(todo, true)}>
					{todo.description}
					<button class="btn-delete" on:click="{() => remove(todo)}"></button>
				</label>
			{/each}
		</div>

		<div class="col-md-6 col-sm-12 c0l-xs-12">
			<h2>Done</h2>
			{#each todos.filter(t => t.done) as todo (todo.id)}
				<label
					class="done"
					in:receive="{{key: todo.id}}"
					out:send="{{key: todo.id}}"
					animate:flip
				>
				<input type=checkbox checked on:change={() => mark(todo, false)}>
				{todo.description}
				<button class="btn-delete" on:click="{() => remove(todo)}"></button>
				</label>
			{/each}
		</div>
	</div>
</div>



<style>
@import url('https://fonts.googleapis.com/css2?family=Poppins:wght@400;500;600&display=swap');

.container {
	font-family: 'Poppins', sans-serif;
}

input {
	padding-left: 16px;
}

input::placeholder {
	color: #c4c4c4;
}

.row {
	margin-top: 30px;
}

h1 {
	font-size: 32px;
	font-weight: 500;
	margin: 20px 0;
}

h2 {
	font-size: 28px;
	font-weight: 500;
	user-select: none;
	margin: 0 0 10px 0;
}

label {
	position: relative;
	line-height: 1.2;
	padding: 0.5em 2.5em 0.5em 2em;
	margin: 0 0 0.5em 0;
	border-radius: 4px;
	user-select: none;
	border: 1px solid hsl(240, 8%, 70%);
	background-color:hsl(240, 8%, 93%);
	color: #333;
}

input:focus {
	outline: none;
}

input[type="checkbox"] {
	position: absolute;
	left: 0.5em;
	top: 0.6em;
	margin: 0;
}

.done {
	border: 1px solid #e3e3e8;
	background-color:#28A745;
	color: #fff;
	transition: .4s;
}

.done:hover {
	background-color:#45a75c;
}

.btn-delete {
	position: absolute;
	top: 0;
	right: 0.2em;
	width: 2em;
	height: 100%;
	background: no-repeat 50% 50% url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 24 24'%3E%3Cpath fill='%23676778' d='M12,2C17.53,2 22,6.47 22,12C22,17.53 17.53,22 12,22C6.47,22 2,17.53 2,12C2,6.47 6.47,2 12,2M17,7H14.5L13.5,6H10.5L9.5,7H7V9H17V7M9,18H15A1,1 0 0,0 16,17V10H8V17A1,1 0 0,0 9,18Z'%3E%3C/path%3E%3C/svg%3E");
	background-size: 1.4em 1.4em;
	border: none;
	opacity: 0;
	transition: opacity 0.2s;
	cursor: pointer;
	fill: #fff;
}

label:hover button {
	opacity: 1;
}

@media (max-width: 767px) {
	label {
		width: 33.33%;
	}
}
</style>
