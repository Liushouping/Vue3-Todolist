<template>
  <div 
  class="bg-gray-50 w-full h-screen flex justify-center items-center">
    <div 
    class="bg-white rounded-lg p-10 w-[480px]">
      <div 
      class="flex flex-col">
        <div class="flex justify-between">
          <label class="text-2xl text-gray-900 font-bold my-2">待辦事項</label>

          <div class="grid grid-rows-2 gap-4">
            <button class="bg-blue-300 px-4 py-1 rounded-lg text-white text-[10px]" @click="removeAllTodos">全部移除</button>
            <button class="bg-red-300 px-4 py-1 rounded-lg text-white text-[10px]" @click="markAllDone">完成／尚未完成</button>
          </div>
        </div>

        <label class="my-2">還有 {{ activeTodosLength() }} 筆任務未完成</label>
        <form @submit.prevent="addNewTodo" class="border flex justify-between p-2">
          <input v-model="newTodo" name="newTodo" class="w-full focus:outline-none focus:border-sky-500">
          <button class="ml-2">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6 text-gray-500" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
              <path stroke-linecap="round" stroke-linejoin="round" d="M12 6v6m0 0v6m0-6h6m-6 0H6" />
            </svg>
          </button>
        </form>
      </div>
  
      <div class="flex justify-between my-4">
        <button class="border border-gray-500 px-8 py-1 rounded-lg text-gray-500 hover:bg-gray-500 hover:text-white" 
        :class="{'active': visibility == 'all'}" @click="visibility='all'">全部</button>
        <button class="border border-gray-500 px-8 py-1 rounded-lg text-gray-500 hover:bg-gray-500 hover:text-white" 
        :class="{'active': visibility == 'done'}" @click="visibility='done'">完成</button>
        <button class="border border-gray-500 px-8 py-1 rounded-lg text-gray-500 hover:bg-gray-500 hover:text-white" 
        :class="{'active': visibility == 'todo'}" @click="visibility='todo'">尚未完成</button>
      </div>
      <ul class="overflow-y-auto h-[210px] pr-8 px-4 my-8">
        <li v-for="(todo, index) in filteredTodos" :key="todo.id" class="todo flex flex-row justify-between py-2">
          <h3 :class="{ done: todo.done }" @click="toggleDone(todo)">{{todo.content}}</h3>
          <button @click="removeTodo(index)">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6 text-gray-500" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
              <path stroke-linecap="round" stroke-linejoin="round" d="M6 18L18 6M6 6l12 12" />
            </svg>
          </button>
        </li>
      </ul>
    </div>
  </div>

</template>

<script>
import { ref,computed } from 'vue';
export default {
  setup() {
    const newTodo = ref('');
    const todos = ref([]);
    const visibility = ref('all');

    function addNewTodo() {
      if (newTodo.value.trim() === '') {
        return
      };

      todos.value.push({
        id: Date.now(),
        done: false,
        content: newTodo.value,
      });

      newTodo.value = '';
    }
    function toggleDone(todo) {
      todo.done = !todo.done;
    }
    function removeTodo(index) {
      todos.value.splice(index, 1);
    }
    function markAllDone() {
      todos.value.forEach((todo) => todo.done =! todo.done);
    }
    function removeAllTodos() {
      todos.value = [];
    }
    function activeTodosLength() {
      return todos.value.filter((todo) => !todo.done).length
    }

    const filteredTodos = computed(() => {
      if(visibility.value == 'all'){
            return todos.value;
        }else if(visibility.value == 'done'){
            var newTodos = [];
            todos.value.forEach(function(todo){
                if(todo.done){
                    newTodos.push(todo);
                }
            })
            return newTodos;
        }else if (visibility.value == 'todo') {
            var newTodos = [];
            todos.value.forEach(function(todo){
                if (!todo.done) {
                    newTodos.push(todo);
                }
            })
            return newTodos;
      }
    })
    return {
      todos,
      newTodo,
      addNewTodo,
      toggleDone,
      removeTodo,
      markAllDone,
      removeAllTodos,
      activeTodosLength,

      visibility,
      filteredTodos,
    };
  }
}
</script>

<style>
/*body {
  font-family: sans-serif;
  padding-top: 1em;
  padding-bottom: 1em;
  font-size: 2em;
  width: 80%;
  margin: 0 auto;
}
input, textarea, button, p, div, section, article, select {
  display: 'block';
  width: 100%;
  font-family: sans-serif;
  font-size: 1em;
  margin: 0.5em;
}*/
.todo {
  cursor: pointer;
}
.done {
  text-decoration: line-through;
  background: #eee;
  border-radius: 10px;
}
</style>