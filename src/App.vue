<template>
  <div id="app">
    <header>
      <nav class="navbar">
        <div class="logo">
          <img src="https://media.geeksforgeeks.org/gfg-gg-logo.svg" alt="GeeksforGeeks logo">
        </div>
      </nav>
    </header>
    <div class="container">
      <h1 class="title">TODO LIST</h1>
      <hr/>
      <div class="input-group">
        <input type="text" class="form-control" placeholder="Add Item..." v-model="userInput" @keyup.enter="addItem">
        <button class="btn btn-success" @click="addItem">ADD</button>
        <button class="btn btn-info" @click="searchItem">Search</button>
      </div>
      <div class="todo-table">
        <div class="table-header">
          <div class="table-cell">Task</div>
          <div class="table-cell">Actions</div>
        </div>
        <div class="table-body">
          <div class="table-row" v-for="(item, index) in filteredList" :key="index" :class="{ 'even-row': index % 2 === 0, 'completed': item.completed }">
            <div class="table-cell" :class="{'completed-cell': item.completed}">{{ item.value }}</div>
            <div class="table-cell"><button class="btn btn-primary" @click="toggleCompleted(index)">{{ item.completed ? 'Undo' : 'Complete' }}</button></div>
            <button class="btn btn-info" @click="editItem(index)">Edit</button>
            <button class="btn btn-danger" @click="deleteItem(index)">Delete</button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { ref, computed, onMounted} from 'vue';
import axios from 'axios';

  export default{
    setup () {
      const userInput = ref('');
      const searchInput = ref('');
      const list = ref([]);
      const apiUrl = `https://jsonplaceholder.typicode.com/todos`;
    
      const fetchTodosFromAPI = async () => {
        try {
          const response = await axios.get(apiUrl);
          list.value = response.data.map(todo => ({
            id: todo.id,
            value: todo.title,
            completed: todo.completed
          }));
        } catch (error) {
          console.error("Error fetching todos:", error);
        }
      };

      const filteredList = computed(() =>
    list.value.filter(item => item.value.toLowerCase().includes(searchInput.value.toLowerCase())
  )
);
      const addItem = () => {
        if (userInput.value.trim() !== '') {
          const newItem = {
            id: Math.random(),
            value: userInput.value.trim(),
            completed: false
          };
          list.value.push(newItem);
          userInput.value='';
        }
      };

      const deleteItem = (index) => {
        list.value.splice(index, 1);
      };

      const editItem = (index) => {
        const editedTodo = prompt('Edit the todo:');
        if (editedTodo !== null && editedTodo.trim() !== '') {
          list.value[index].value = editedTodo.trim();
        }
      };

      const toggleCompleted = (index) => {
        list.value[index].completed = !list.value[index].completed;
      };

      const searchItem = () => {
        const query = userInput.value;
        if (query !== null) {
          searchInput.value = query.trim();
        }
      };

      onMounted(fetchTodosFromAPI);

      return {
        userInput, 
        searchInput,
        list,
        filteredList,
        addItem,
        deleteItem, 
        editItem,
        toggleCompleted,
        searchItem
      };
    }
  };
</script>

<style src="./assets/style.css">
</style>