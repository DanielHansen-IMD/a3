<script>
     import { fly } from 'svelte/transition';
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


<div class="frame">
     <h1>Giterdone</h1>
     <form on:submit|preventDefault={addToArray}>
          <input type="text" bind:value={todoItem}>
          <button type="submit">Add</button>
     </form>

     <ul>
          {#each $todoList as item, index}
               <li transition:fly={{y: 15, duration: 200}} class:done={item.done}>
                    <input type="checkbox" bind:checked={item.done} on:change={updateList}>
                    <span class="text">{item.text}</span>
                    <span on:click={() => removeThis(index)} class="remove" role="button" tabindex="0">&times;</span>
               </li>
          {/each}
     </ul>
     <div class="button-group">
          {#if isDone.length > 0}
               <button on:click={clearDone}>Remove Done</button>
          {/if}
          {#if $todoList.length > 0} 
               <button class="clear-list" on:click={clearAll}>Clear List</button>
          {/if}
     </div>
</div>

<style>
     h1 {
          font-weight: 300;
          color: #f8f8f8;
          font-size: 4.5rem;
          margin: 0;     
          letter-spacing: -0.06em;
          line-height: 1;
          margin-bottom: 0.7em;
     }
     input, button {
          font-family: sans-serif;
          font-family: "Work Sans", sans-serif;
     }
     ul {
          list-style: none;
          padding: 0;
     }
     .frame {
          max-width: 600px;
          margin: 5vw auto;
     }
     .text {
          font-weight: 600;
          margin-left: 2vw;
     }
     form {
          display: flex;
     }
     form input[type="text"] {
          padding: 1em;
          font-size: 1.2rem;
          border: none;
          border-top-left-radius: 50px;
          border-bottom-left-radius: 50px;
          width: 80%;
     }
     form button {
          width: 20%;
          border: none;
          border-top-left-radius: 0;
          border-top-right-radius: 50px;
          border-bottom-left-radius: 0;
          border-bottom-right-radius: 50px;
          font-size: 1.2em;
          color: #f8f8f8;
          background-color:rgb(20, 153, 95);
          cursor: pointer;
     }
     li {
          font-size: 1.3rem;
          display: block;
          width: 100%;
          background-color: rgb(243, 201, 207); 
          margin-bottom: 4px;
          padding: 2.5vw;
          border-radius: 2em;
          border-top-right-radius:0px;
          position: relative;
          display: flex;
          align-items: center;
          box-sizing: border-box;
     }
     li input[type="checkbox"] {
          width: 2em;
          height: 2rem;
          accent-color: rgb(20, 153, 95);
     }
     li.done {
          opacity: 0.5;
     }
     .done .text {
          color: #777;
          text-decoration: line-through;

     }
     .remove {
          color: darkred;
          cursor: pointer;
          position: absolute;
          right: 12px;
          top: 8px;
          line-height: 1;
          font-size: 2rem;
     }
     .button-group {
          display: flex;
          justify-content: flex-end;
     }
     .button-group button {
          font-size: 1.2rem;
          background-color: rgb(20, 153, 95);
          color: #f8f8f8;
          padding: 0.8em 1.4em;
          border: none;
          border-radius: 50px;
          margin-left: 0.7em;
          cursor: pointer;
     }
     .button-group .clear-list {
          background-color: #999;
     }
     @media screen and (max-width: 550px) {
          input[type="text"], form button, .button-group button, li {
               font-size: 1em;
          }
          form input[type="text"] {
               border-top-left-radius: 20px;
               border-bottom-left-radius: 20px;
          }
          form button {
               border-top-right-radius: 20px;
               border-bottom-right-radius: 20px;
          }
          li {
               padding: 4vw;
               border-radius: 20px;
               border-top-right-radius: 0;
          }
          .remove {
               top: 5px;
               right: 10px;
          }
     }
</style>