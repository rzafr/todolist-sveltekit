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
        // Convert the 'todos' array into a JSON string and store it in localStorage
		localStorage.setItem('todos', JSON.stringify(todos));
	})

    function addTodo(event: KeyboardEvent): void {
        // Only proceed if the pressed key is "Enter"
        if (event.key !== 'Enter') return;

        // Cast the event target to an input element
        const todoEl = event.target as HTMLInputElement;

        // Get the input value (the task text)
        const text = todoEl.value.trim();

        // Set the default state of the task as not done
        const done = false;

        // Add the new task to the existing list of todos
        todos = [...todos, { text, done }];

        // Clear the input field
        todoEl.value = '';
    }

    function toggleTodo(event: Event, index: number): void {
        // Cast the event target to an input element
		const inputEl = event.target as HTMLInputElement;

		// Create a new array with updated task
        todos = todos.map((todo, i) =>
            i === index ? { ...todo, done: inputEl.checked } : todo
        );
	}
</script>

<div class="container">
    <h1>Lista de Tareas</h1>
    <div class="input-container">
        <input type="text" onkeydown={addTodo} placeholder="Agregar tarea" maxlength="20" />
    </div>
    <div class="todos">
        {#if todos.length === 0}
            <p class="empty">No hay tareas en este momento.</p>
        {:else}
            <table>
                <thead>
                    <tr>
                        <th>Tarea</th>
                        <th>Completar</th>
                        <th>Estado</th>
                    </tr>
                </thead>
                <tbody>
                    {#each todos as todo, i}
                        <tr class:completed={todo.done}>
                            <td class:completed={todo.done}>
                                {todo.text}
                            </td>
                            <td>
                                <input type="checkbox" 
                                    onchange={(event) => toggleTodo(event, i)} 
                                    data-index={i} 
                                    checked={todo.done} />
                            </td>
                            <td>
                                {todo.done ? 'Tarea completada' : 'Tarea pendiente'}
                            </td>
                        </tr>
                    {/each}
                </tbody>
            </table>
        {/if}    
    </div>
</div>

<style>
    :global(body) {
        font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        background-color: #f5f7fa;
        color: #333;
        margin: 0;
        padding: 0;
        display: flex;
        justify-content: center;
        align-items: flex-start;
        min-height: 100vh;
    }

    .container {
        width: 100%;
        max-width: 600px;
        padding: 2rem;
        margin-top: 2rem;
        background-color: white;
        border-radius: 1rem;
        box-shadow: 0 10px 25px rgba(0, 0, 0, 0.1);
    }

    h1 {
        text-align: center;
        margin-bottom: 1rem;
        color: #4f46e5;
    }

    .input-container {
        display: flex;
        gap: 0.5rem;
        margin-bottom: 1rem;
    }

    .input-container input[type='text'] {
        flex: 1;
        padding: 0.75rem 1rem;
        border: 2px solid #ddd;
        border-radius: 0.5rem;
        outline: none;
        transition: border 0.2s;
    }

    .input-container input[type='text']:focus {
        border-color: #4f46e5;
        box-shadow: 0 0 0 3px rgba(79, 70, 229, 0.2);
    }

	.todos {
		margin-top: 1rem;
	}

    .empty {
        text-align: center;
        color: #888;
        padding: 1rem;
        background-color: #f0f0f5;
        border-radius: 0.5rem;
    }

    table {
        width: 100%;
        border-collapse: collapse;
    }

    thead {
        background-color: #4f46e5;
        color: white;
    }

    th,
    td {
        padding: 0.75rem;
        text-align: left;
    }

    tr {
        transition: background-color 0.2s;
    }

    tr.completed {
        color: #888;
        background-color: #e8f5e9;
    }

    tr.completed td {
        color: #888;
    }

    td.completed {
        text-decoration: line-through;
    }

    input[type='checkbox'] {
        transform: scale(1.2);
        cursor: pointer;
    }

    @media (max-width: 600px) {
        .container {
            padding: 1rem;
            border-radius: 0.75rem;
        }

        .input-container input[type='text'] {
            font-size: 0.95rem;
            padding: 0.6rem 0.9rem;
        }

        table,
        thead,
        tbody,
        th,
        td,
        tr {
            display: block;
            width: 95%;
        }

        thead {
            display: none;
        }

        tr {
            background-color: white;
            margin-bottom: 1rem;
            padding: 1rem;
            border: 1px solid #ddd;
            border-radius: 0.5rem;
        }

        td {
            display: flex;
            justify-content: space-between;
            padding: 0.5rem 0;
            border-bottom: 1px solid #eee;
        }

        td:last-child {
            border-bottom: none;
        }
    }
</style>