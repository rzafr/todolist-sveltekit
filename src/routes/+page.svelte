<script lang="ts">
    type Todo = {
		text: string,
		done: boolean
	}

    let todos = $state<Todo[]>([]);

    $effect(() => {
        // Retrieve the 'todos' item from localStorage and store it in the variable savedTodos
		const savedTodos = localStorage.getItem('todos');

        // If there are saved todos in localStorage, parse them from JSON and assign to the todos array
        if (savedTodos) {
            todos = JSON.parse(savedTodos);
        }
	});

    $effect(() => {
		localStorage.setItem('todos', JSON.stringify(todos));
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

    function toggleTodo(event: Event, index: number) {
        // Cast the event target to an input element
		const inputEl = event.target as HTMLInputElement;

		// Create a new array with updated task
        todos = todos.map((todo, i) =>
            i === index ? { ...todo, done: inputEl.checked } : todo
        );
	}
</script>

<input type="text" onkeydown={addTodo} placeholder="Agregar tarea" />

<div class="todos">
    <table>
        <thead>
          <tr>
            <th>Tarea</th>
            <th>Completada</th>
            <th>Estado</th>
          </tr>
        </thead>
        <tbody>
          {#each todos as todo, i}
            <tr>
              <td>
                <p>{todo.text}</p>
              </td>
              <td>
                <input type="checkbox" 
                       onchange={(event) => toggleTodo(event, i)} 
                       data-index={i} 
                       checked={todo.done} />
              </td>
              <td>
                {#if todo.done}
                  <p>Tarea completada</p>
                {:else}
                  <p>Tarea pendiente</p>
                {/if}
              </td>
            </tr>
          {/each}
        </tbody>
    </table>
</div>

<style>
	.todos {
		display: grid;
		gap: 1rem;
		margin-block-start: 1rem;
	}

    input[type='text'] {
		width: 70%;
		padding: 1rem;
	}
</style>