<script>
  import { onMount } from 'svelte';
  import { deleteData, getData, setData } from '../helper/localstorage';    //importing getData and setData from helper folder
  import { ADD_TODO } from "../config";
  import { DELETE_TODO } from "../config";
  import { EDIT_TODO } from "../config";
  import { GET_TODO } from "../config";
  
  

  onMount(() => {
    console.log("getData",getData())

  });

  $: todos = [];    //$: To make it reactive 

  //Fetching add-todo
  let title = "";
  const handleAddTodoBtn = async() => {
      fetch(ADD_TODO, {
          method:"POST",
          body:JSON.stringify({
              title,    //Inserting data into title = ""
          }),
      })
      .then((res) => res.json())
      .then((data) => {
      console.log("Response Data:", data);
      todos = [...todos, data];   //Adding data in todos = [] to display in Todo List
      title = "";   //To show the placeholder as null in the input text
    });
  }


  //Fetching get-todo
  const getTodo = () => {
  fetch(GET_TODO)
    .then((res) => res.json())
    .then((data) => {
      console.log("Data: ", data);
      todos = data;   //inserting data into todos = []
    });
  };
  getTodo();    //Calling Function


  //Fetching delete-todo
  const handleDeleteTodoBtn = (id) => {
  fetch(DELETE_TODO, {
    method: "POST",
    body: JSON.stringify({
      id: id,
    }),
  })
  .then((res) => res.json())
  .then((data) => {
      console.log("Data:", data);

      //Disaapering data from Todo List 
      let new_todos = todos.filter((item) => item._id !== id);
      todos = new_todos;
    });
  };

  let editTodoId = "";
  const handleEditBtnClick = (id) => {
  editTodoId = id;

  let todo = todos.find((item) => item._id === id);
  console.log("Todo: ", todo);
  title = todo.title;   //After clicking on Edit data will move to input placeholder
  };


  const haneleEditSubmitBtnClick = () => {
  fetch(EDIT_TODO, {
    method: "POST",
    body: JSON.stringify({
      id: editTodoId,
      title: title,
    }),
  })
  .then((res) => res.json())
  .then((data) => {
  console.log("Data: ", data);
  todos = todos.map((item) => {
      if (item._id === editTodoId) {
          item.title = data.title;
          }
          return item;
          });
          title = "";
          editTodoId = "";
      });
  };

const handleLogout=()=>{
  deleteData()
  location.reload()
}


</script>



<main>
  <div class="flex justify-end">
    <button on:click={()=>handleLogout("LOGIN")} class="rounded-md text-blue-900 text-center sm:text-left sm:text-lg md:text-xl lg:text-2xl sm:pt-1 mt-1 px-1 border-2 lg:pb-1 mr-1 border-sky-900">Logout</button>
  </div>
  <div class="mx-1 pt-[40px] lg:m-[10px] lg:p-[20px]">
      <p class="text-center text-blue-900 font-bold text-[40px] underline decoration-solid decoration-sky-500">ToDo List</p>
      <div class="flex justify-center mt-20">
          <p class="rounded-md text-blue-900 font-bold text-center sm:text-left sm:text-lg md:text-xl lg:text-2xl sm:pt-1 pt-1 mr-3 border-2 px-2 border-sky-500">Enter your List</p>
          <input type="text" bind:value={title} class="sm:text-xs lg:text-2xl text-gray-900 rounded-lg w-[50vw] p-2 mr-3 border-2 border-sky-500" placeholder="Add Todo"/>
          {#if editTodoId.length}
          <button type="button" class="sm:text-xs lg:text-2xl px-2 my-1 border-2 border-blue-900 rounded-md" on:click={haneleEditSubmitBtnClick}>Done</button>
          {:else}
          <button class="sm:text-xs lg:text-2xl px-2 border-2 border-blue-900 rounded-md" on:click={handleAddTodoBtn} type="button">Save</button>
          {/if}
      </div>
  </div>
  
  {#each todos as toDo, i}
      <div class="mt-[20px] flex justify-around w-[100vw]">
          <div class="w-[50vw] flex">
              <span class="sm:text-xs lg:text-2xl mr-2 py-2">{i+1}.</span>
              <span class="sm:text-xs lg:text-2xl w-[50vw] py-2">{toDo.title}</span>
              </div>
          <div>
              <button class="sm:text-xs lg:text-2xl px-2 my-1 border-2 border-blue-900 rounded-md" on:click={(e) => handleEditBtnClick(toDo._id)}>Edit</button>
              <button class="sm:text-xs lg:text-2xl ml-2 px-2 my-1 border-2 border-blue-900 rounded-md" on:click={(e) => handleDeleteTodoBtn(toDo._id)}>Delete</button>
          </div>
      </div>
  {/each}
</main>
