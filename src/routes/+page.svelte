<script>
     import '../style.css';
     import { writable } from 'svelte/store';
     let todoList = writable([]);
     let todoItem, storedList;
     
     if (typeof window !== 'undefined' && typeof localStorage !== 'undefined') {
          storedList = localStorage.getItem('storedList');
          if(storedList) {
               $todoList = (JSON.parse(storedList));
          }
     }
     
     function updateList() {
          return storedList = localStorage.setItem('storedList', JSON.stringify($todoList));
     }

     $: isDone = $todoList.filter(item => item.done);

     function addToArray() {
          if (todoItem == '') {
               return;
          }
         $todoList = [...$todoList, {
               text: todoItem,
               done: false
          }];
          updateList();
          todoItem = '';
     }
     function removeThis(index) {
          $todoList.splice(index, 1);
          $todoList = $todoList;
          updateList();
     }
     function clearDone() {
          $todoList = $todoList.filter(item => !item.done)
          updateList();
     }
     function clearAll() {
          $todoList = [];
          localStorage.clear();
     }
</script>

<h1>Todo!</h1>
<form on:submit|preventDefault={addToArray}>
     <input type="text" bind:value={todoItem}>
     <button type="submit">Add</button>
</form>

<ul>
     {#each $todoList as item, index}
          <li>
               <input type="checkbox" bind:checked={item.done} on:change={updateList}>
               <span class:done={item.done} >{item.text}</span>
               <span on:click={() => removeThis(index)} class="remove" role="button" tabindex="0">&times;</span>
          </li>
     {/each}
</ul>

{#if isDone.length > 0}
<button on:click={clearDone}>Remove Done</button>
{/if}
{#if $todoList.length > 0} 
<button on:click={clearAll}>Clear List</button>
{/if}

<style>
     ul {
          list-style: none;
     }
     li {
          font-size: 1.3rem;
     }
     .done {
          color: #ccc;
          text-decoration: line-through;
     }
     .remove {
          color: darkred;
          cursor: pointer;
     }
</style>