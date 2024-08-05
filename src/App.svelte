<script lang="ts">
  import "./app.css";
  import { onMount } from 'svelte';
  import axios from 'axios';

  const api_url = import.meta.env.VITE_API_ENDPOINT;
  let todos : {id: number,title: string, description: string, completed: boolean}[] = []
  let todoInput : {title: string, description: string} = {title: "", description: ""}
  let todoRequestBody : {title: string, description: string, due_date: string}

  async function fetchTodos() {
    let response = axios.get(`${api_url}/todos?limit=10&offset=0`)
    todos = (await response).data
  }

  async function addTodo() {
    todoRequestBody = {
      title: todoInput.title,
      description: todoInput.description,
      due_date: "2024-08-25"
    }
    let response = axios.post(`${api_url}/todo`, todoRequestBody)
    todos = [...todos, (await response).data]
  }

  async function deleteTodo(id: number) {
    let deletedTodo = axios.delete(`${api_url}/todo/${id}`) 
    todos = todos.filter((todo) => {
      return todo.id != id
    })
  }

  onMount(() => {
    fetchTodos()
  })
</script>

<main class="container mx-auto p-4">
  <div class="mb-4">
    <input type="text" bind:value={todoInput.title} placeholder="Title" class="border rounded w-full py-2 px-3 mb-2">
    <input type="text" bind:value={todoInput.description} placeholder="Description" class="border rounded w-full py-2 px-3 mb-2">
    <button class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded" on:click={addTodo}>Create</button>
  </div>
  <div>
    {#each todos as todo, id}
      <div class="bg-white shadow-md rounded p-4 mb-4">
        <div class="font-bold text-xl mb-2">{todo.title}</div>
        <div class="text-gray-700 text-base">{todo.description}</div>
        <div class="text-gray-700 text-base">
          Completed: 
          {#if todo.completed == true}
            <span class="bg-green-100 text-green-800 text-xs font-semibold mr-2 px-2.5 py-0.5 rounded">Yes</span>
          {:else}
            <span class="bg-red-100 text-red-800 text-xs font-semibold mr-2 px-2.5 py-0.5 rounded">No</span>
          {/if}
        </div>
        <button class="bg-red-500 hover:bg-red-700 text-white font-bold py-2 px-4 rounded mt-2" on:click={() => {deleteTodo(todo.id)}}>Delete</button>
      </div>
    {/each}
  </div>
</main>