<script lang="ts">
    let todos = $state([
        { text: 'Todo 1', done: false },
        { text: 'Todo 2', done: false },
    ]);

    $effect(() => {
		console.log(todos);
	})

    function addTodo(event: KeyboardEvent) {
        // Only proceed if the pressed key is "Enter"
        if (event.key !== 'Enter') 
            return;

        // Cast the event target to an input element
        const todoEl = event.target as HTMLInputElement;

        // Get the input value (the task text)
        const text = todoEl.value;

        // Set the default state of the task as not done
        const done = false;

        // Add the new task to the existing list of todos
        todos = [...todos, { text, done }];

        // Clear the input field
        todoEl.value = '';
    }

    function toggleTodo(event: Event) {
        // Cast the event target to an input element
		const inputEl = event.target as HTMLInputElement;

        // Extract the index from the dataset (it's a string, so we convert it)
		const index = +inputEl.dataset.index!;

		// Create a new array with updated task
        todos = todos.map((todo, i) =>
            i === index ? { ...todo, done: !todo.done } : todo
        );
	}
</script>

<input onkeydown={addTodo} placeholder="Add todo" type="text" />

<div class="todos">
    {#each todos as todo, i}
        <div class="todo">
            <input value={todo.text} type="text">
            <input onchange={toggleTodo} data-index={i} checked={todo.done} type="checkbox">
        </div>    
    {/each}
</div>

<style>
	.todos {
		display: grid;
		gap: 1rem;
		margin-block-start: 1rem;
	}

	.todo {
		position: relative;
	}

    input[type='text'] {
		width: 100%;
		padding: 1rem;
	}

	input[type='checkbox'] {
		position: absolute;
		right: 4%;
		top: 50%;
		translate: 0% -50%;
	}
</style>